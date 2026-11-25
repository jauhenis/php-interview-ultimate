---
title: 'Redis: Caching Patterns, Persistence and Scaling'
slug: '/answers/redis'
---

# Redis: Caching Patterns, Persistence and Scaling

[Redis documentation](https://redis.io/docs/latest/)

Redis is an in-memory key-value store. Besides caching, PHP applications use it to store sessions (`session.save_handler = redis` with the phpredis extension), as a queue backend (Laravel queues, Symfony Messenger), for counters and rate limiting (`INCR`), for distributed locks (`SET key token NX PX 30000`) and for messaging (Pub/Sub, Streams).

The PHP examples below use core PHP 8.1+ with the [phpredis](https://github.com/phpredis/phpredis) extension (`ext-redis`), without a framework. `$redis` is a connected `Redis` instance:

```php
$redis = new Redis();
$redis->connect('127.0.0.1', 6379);
```

## Expiration (TTL)

Set a lifetime with `SET key value EX 60`, `EXPIRE key 60`, `PEXPIRE` (milliseconds) or `EXPIREAT` (Unix timestamp). `TTL key` returns the remaining seconds, `-1` if the key has no expiration and `-2` if the key does not exist. `PERSIST` removes the expiration. A plain `SET` on an existing key also removes its TTL unless you pass `KEEPTTL`.

Redis deletes expired keys in two ways. When a client accesses an expired key, Redis deletes it and returns nothing. A background cycle also samples keys that have a TTL and deletes the expired ones. An expired key can therefore occupy memory for a short time.

## Why Redis is fast

Data lives in RAM, so reads need no disk I/O. One thread executes commands one at a time, so commands need no locks and each command is atomic. I/O multiplexing (epoll or kqueue) lets this thread serve thousands of connections. Since Redis 6, the `io-threads` setting moves network reads and writes to extra threads, but commands still run one at a time. A slow command such as `KEYS *` or `HGETALL` on a huge hash delays every other client.

## Caching strategies

- **Cache-aside (lazy loading):** the application reads from the cache. On a miss, it loads the data from the database and writes it to the cache. Only requested data is cached, but it can be stale until the TTL expires.
- **Read-through:** the cache layer loads missing data itself, and the application talks only to the cache.
- **Write-through:** every write updates the cache and the database synchronously. The cache stays fresh, but writes are slower.
- **Write-behind (write-back):** writes go to the cache and reach the database later, often in batches. Writes are fast, but data is lost if the cache fails before the flush.
- **Refresh-ahead:** frequently read keys are refreshed in the background before they expire.

Cache-aside with phpredis and PDO:

```php
function findUser(Redis $redis, PDO $pdo, int $id): ?array
{
    $key = "user:{$id}";

    $cached = $redis->get($key);
    if ($cached !== false) {
        return json_decode($cached, true);
    }

    $stmt = $pdo->prepare('SELECT id, name, email FROM users WHERE id = ?');
    $stmt->execute([$id]);
    $user = $stmt->fetch(PDO::FETCH_ASSOC) ?: null;

    if ($user !== null) {
        $redis->set($key, json_encode($user), ['ex' => 3600]);
    }

    return $user;
}
```

Frameworks implement the same pattern: `Cache::remember()` in Laravel, and `CacheInterface::get($key, $callback)` in the Symfony Cache component.

## Cache invalidation

- **TTL:** data may be stale for up to TTL seconds.
- **Delete on write:** after the database transaction commits, delete the key instead of writing the new value. If two writers update the cache concurrently, the older value can be written last. A delete cannot leave an older value behind.
- **Versioned keys:** include a version in the key, for example `catalog:v42:product:1`. Incrementing the version makes the old keys unreachable, and their TTL removes them.
- **Tags:** group keys by tag and invalidate the whole group, for example with `TagAwareCacheInterface::invalidateTags()` in Symfony or `Cache::tags()` in Laravel.

A reader can still load the old row before the commit and write it to the cache after the delete. A short TTL limits how long such a value stays in the cache.

## Memory limits and eviction

When memory usage reaches `maxmemory`, Redis applies `maxmemory-policy`:

- `noeviction` (default): writes fail with an OOM error, reads still work.
- `allkeys-lru` / `volatile-lru`: evict the least recently used key among all keys or among keys with a TTL.
- `allkeys-lfu` / `volatile-lfu`: evict the least frequently used key.
- `allkeys-random` / `volatile-random`: evict a random key.
- `volatile-ttl`: evict the key with the shortest remaining TTL.

A Redis instance used only as a cache usually uses `allkeys-lru` or `allkeys-lfu`. If the same instance stores sessions, queues or locks, eviction can delete them. Use a separate instance, or a `volatile-*` policy with a TTL set only on cache keys. If no key has a TTL, the `volatile-*` policies behave like `noeviction`.

## Persistence: RDB and AOF

- **RDB:** Redis periodically writes a snapshot of the whole dataset from a forked child process (`save` rules, `BGSAVE`). Files are compact and load quickly, but writes since the last snapshot are lost. Forking a large dataset can cause latency spikes.
- **AOF:** Redis appends every write command to a log. `appendfsync` controls durability: `always` is the safest and slowest, `everysec` (default) loses at most about one second, and `no` lets the OS decide. A background rewrite compacts the log.
- **RDB and AOF together:** with `aof-use-rdb-preamble yes` (default since Redis 5), a rewritten AOF starts with an RDB snapshot, so restarts are faster.
- **No persistence:** acceptable for a cache that can be rebuilt from the database.

Replication is asynchronous, so even with `appendfsync always`, Redis should not hold the only copy of critical data.

## Commands that block Redis

`KEYS` scans the whole keyspace in one call. With millions of keys, it can block the server for seconds. `SCAN` iterates with a cursor and returns small batches. It may return a key more than once, and keys added or removed during the iteration may be missing from the results. `HSCAN`, `SSCAN` and `ZSCAN` iterate over the members of a single hash, set and sorted set.

Deleting keys by pattern with phpredis:

```php
$redis->setOption(Redis::OPT_SCAN, Redis::SCAN_RETRY);

$iterator = null;
while ($keys = $redis->scan($iterator, 'session:*', 1000)) {
    $redis->unlink($keys);
}
```

Other commands that can block Redis: `DEL` on a huge key (use `UNLINK`, which frees memory in a background thread), `FLUSHALL` / `FLUSHDB` without `ASYNC`, and `HGETALL`, `SMEMBERS` or `LRANGE 0 -1` on large collections. `redis-cli --bigkeys` finds large keys, and `SLOWLOG GET` lists slow commands.

## Pipelining and transactions

- **Pipelining** sends many commands without waiting for each reply. It saves network round trips, but commands from other clients can run in between.
- **`MULTI`/`EXEC`** queues commands and runs them as one isolated block. If a command fails at runtime, for example `INCR` on a non-numeric value, the other commands are still applied. Errors detected while queueing, such as a wrong number of arguments, abort the whole transaction.
- **`WATCH`** aborts the transaction if a watched key changes before `EXEC`, and the client retries.
- **Lua scripts** (`EVAL`) run atomically on the server and suit logic that reads a value, decides and writes.

With phpredis:

```php
// Pipelining: one network round trip
$pipe = $redis->pipeline();
foreach ([1, 2, 3] as $id) {
    $pipe->get("user:{$id}");
}
$users = $pipe->exec();

// Transaction: both commands run as one block
$pageId = 42;
$redis->multi()
    ->incr("views:{$pageId}")
    ->expire("views:{$pageId}", 86400)
    ->exec();
```

## PHP clients: phpredis and Predis

- **phpredis** is a PHP extension written in C (`pecl install redis`). It is faster and supports persistent connections, binary serializers and compression. It must be installed on every server and Docker image.
- **Predis** is a Composer package written in PHP (`composer require predis/predis`). It needs no extension but is slower.

Laravel and the Symfony Cache component support both clients.

## Cache stampede

When a frequently read key expires, many concurrent requests miss the cache at once and all run the same expensive query. Ways to prevent it:

- **Lock:** only the process that acquires `SET lock:key token NX EX 10` rebuilds the value. The others wait briefly or return the old value. Laravel provides `Cache::lock()`, and the Symfony Cache component locks the computation of a missing value automatically.
- **Probabilistic early expiration:** a request may rebuild the value before its TTL expires, with a probability that grows as expiration approaches. The Symfony Cache component supports this through the `$beta` argument of `get()`.
- **Stale-while-revalidate:** serve the old value while it is refreshed in the background, for example with `Cache::flexible()` in Laravel 11+.
- **Background refresh:** a scheduled job refreshes the key before it expires.

## Cache penetration, breakdown and avalanche

- **Penetration:** requests for data that does not exist, such as random IDs from a bot, never hit the cache and always reach the database. Cache the empty result with a short TTL, validate input, or check IDs against a Bloom filter.
- **Breakdown:** a single frequently read key expires and causes a stampede. See the previous section.
- **Avalanche:** many keys expire at once because they were written with the same TTL, or a cache node restarts empty. Add random jitter to TTLs (`3600 + random_int(0, 300)`), use replicas and persistence, and warm up the cache gradually.

## Sentinel and Cluster

Both use asynchronous replication from a master to replicas, so a write acknowledged by the master can be lost if the master fails before replicating it.

- **Sentinel** monitors one master and its replicas. When enough Sentinel processes agree that the master has failed, they promote a replica and tell clients its address. Sentinel does not shard data, so the whole dataset must fit on one master.
- **Cluster** splits the keyspace into 16384 hash slots distributed among several masters, each with replicas, and handles failover itself. Multi-key commands, transactions and Lua scripts work only when all keys are in the same slot. Hash tags put related keys in one slot: in `{user:42}:profile` and `{user:42}:cart`, only `user:42` is hashed. Cluster supports only database 0, and clients must follow `MOVED` and `ASK` redirects. In phpredis, use the `RedisCluster` class.

Choose Sentinel when the data fits on one node and you need automatic failover. Choose Cluster when you need to scale memory or write throughput across nodes.

## Rate limiting

- **Fixed window:** `INCR` a key such as `rate:{userId}:{minute}` and set its TTL on the first request. It allows up to twice the limit around a window boundary.
- **Sliding window log:** a sorted set of request timestamps. Remove old entries with `ZREMRANGEBYSCORE`, count the rest with `ZCARD` and add the new request with `ZADD`. It is precise, but memory grows with the number of requests.
- **Token bucket:** smooth limiting with constant memory, usually implemented in Lua.

`INCR` and `EXPIRE` must run atomically. If the process stops between them, the key never expires and the user stays blocked. A fixed window in a Lua script with phpredis:

```php
$script = <<<'LUA'
local current = redis.call('INCR', KEYS[1])
if current == 1 then
    redis.call('EXPIRE', KEYS[1], ARGV[1])
end
return current
LUA;

$userId = 42;
$key = sprintf('rate:%d:%d', $userId, intdiv(time(), 60));
$count = $redis->eval($script, [$key, 60], 1);

if ($count > 100) {
    http_response_code(429);
    exit;
}
```

A Lua script blocks Redis while it runs, so keep scripts short. Laravel provides the `RateLimiter` facade, and Symfony provides the RateLimiter component. Both can store their state in Redis.

## Pub/Sub and Streams

- **Pub/Sub:** `PUBLISH` delivers a message only to subscribers connected at that moment. Redis does not store the message, so an offline subscriber misses it. It suits real-time notifications and cache invalidation across servers.
- **Streams:** `XADD` appends entries to a persistent log. Consumer groups (`XREADGROUP`) distribute entries among workers, and each worker acknowledges an entry with `XACK`. Unacknowledged entries stay pending and can be claimed by another worker with `XAUTOCLAIM`. A message can be delivered more than once, so handlers must be idempotent. Limit the stream length with `MAXLEN` or `MINID`. The Symfony Messenger Redis transport uses Streams.

Subscribing and blocking reads must run in a long-running CLI worker, not in a PHP-FPM request.
