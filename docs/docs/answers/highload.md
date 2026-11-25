---
title: 'Highload & Scalability in Practice'
slug: '/answers/highload'
---

# Highload & Scalability in Practice

This guide covers the practical side of running a PHP application under high load. Architecture topics such as microservices, CQRS and sharding are covered in [Architecture & Highload](architecture_highload.md).

## CDN

A CDN (Content Delivery Network) is a network of edge servers that cache content close to users. Requests for cached files are served by the nearest edge server instead of your origin server.

- **Lower latency:** JS, CSS, images and video come from a nearby location.
- **Less load on the origin:** most requests for static files never reach your servers.
- **Extra protection:** most CDNs also offer DDoS mitigation, TLS termination and a web application firewall.

HTTP headers control caching, for example `Cache-Control: public, max-age=31536000, immutable`. Put a content hash in static file names (`app.3f9a1c.js`), so a file can be cached for a year and each deploy references new names. A CDN can also cache public HTML pages and API responses with `s-maxage`, but must never publicly cache personalized responses.

## Stateless Application Servers

A stateless server keeps no data between requests in its memory or on its local disk. Any request can then go to any server, and a load balancer can add or remove servers freely.

| State           | Where to keep it                                                                          |
| --------------- | ----------------------------------------------------------------------------------------- |
| Sessions        | Redis, Memcached or the database, or a signed token such as a JWT                         |
| Uploaded files  | Object storage such as S3 or MinIO                                                        |
| Cache           | A shared cache such as Redis. APCu only for data that may differ slightly between servers |
| Scheduled tasks | One scheduler, or a distributed lock so a task does not run on every server               |
| Logs            | A central system such as ELK or Loki                                                      |

PHP-FPM follows a share-nothing model: each request starts with empty memory. This is one reason PHP applications scale horizontally easily.

## Background Queues

A web request should do only the work the user has to wait for. Sending emails, generating PDFs, resizing images and calling slow external APIs belong in a queue processed by workers:

- The user gets a response immediately.
- If an external API is down, the job is retried later instead of failing the request.
- Traffic spikes fill the queue instead of overloading the servers.
- Workers scale separately from web servers.

In PHP, common options are Laravel Queues and Symfony Messenger, with RabbitMQ, Redis, Amazon SQS or Kafka as the transport. Long-running workers are not limited by PHP-FPM timeouts, but they can leak memory, so restart them periodically.

## Tuning PHP-FPM

Key settings in a pool configuration file such as `www.conf`:

```ini
pm = static
pm.max_children = 100
pm.max_requests = 1000
request_terminate_timeout = 30s
request_slowlog_timeout = 5s
slowlog = /var/log/php-fpm/slow.log
pm.status_path = /fpm-status
```

- **`pm`:** `static` keeps a fixed number of workers and suits dedicated servers. `dynamic` keeps a range of idle workers. `ondemand` starts workers only when requests arrive and suits low-traffic sites.
- **`pm.max_children`:** the maximum number of requests processed in parallel. Estimate it as the memory available for PHP divided by the average memory of one worker: 8 GB / 80 MB is about 100 workers. Too many workers cause swapping, and too few make requests wait.
- **`pm.max_requests`:** restarts a worker after this many requests, which limits the effect of memory leaks.
- **`request_terminate_timeout`:** stops requests that run too long.
- **`request_slowlog_timeout`** and **`slowlog`:** log a stack trace for requests slower than the threshold.

On the status page, a non-zero `listen queue` and a growing `max children reached` mean all workers are busy and requests are waiting. Also enable OPcache with `opcache.validate_timestamps=0` in production and the optimized Composer autoloader. If workers mostly wait for the database or APIs, more workers help. If the CPU is at 100%, you need more servers or faster code.

## Replication Lag

With primary-replica replication, writes go to the primary and reads are spread across replicas. Replication is usually asynchronous, so replicas lag behind by milliseconds or, under heavy load, by seconds. A user who updates their profile can then read the old version from a replica.

- **Read from the primary after a write:** for the rest of the request, or for a few seconds for that user. Laravel does the former with `'sticky' => true` in `config/database.php`.
- **Read critical data from the primary:** balances, order status after payment and data used for a following write.
- **Wait for the replica:** MySQL can wait for a GTID with `WAIT_FOR_EXECUTED_GTID_SET()`, and ProxySQL can route reads only to replicas with low lag.
- **Semi-synchronous replication:** the primary waits until at least one replica has received the write. It reduces data loss on failover but adds write latency.
- **Monitor the lag:** `Seconds_Behind_Source` in MySQL and `pg_stat_replication` in PostgreSQL.

## Keyset Pagination

`ORDER BY id LIMIT 20 OFFSET 1000000` makes the database read and discard a million rows before returning 20. Rows inserted or deleted between page loads also shift the results.

Keyset (cursor) pagination continues from the last seen sort value. The queries below work in MySQL 8.0+ and PostgreSQL:

```sql
-- First page
SELECT id, title, created_at FROM posts
ORDER BY created_at DESC, id DESC
LIMIT 20;

-- Next page: values from the last row of the previous page
SELECT id, title, created_at FROM posts
WHERE (created_at, id) < (:last_created_at, :last_id)
ORDER BY created_at DESC, id DESC
LIMIT 20;
```

With an index on `(created_at, id)`, every page costs the same. The sort must be unique, so `id` breaks ties. You cannot jump to an arbitrary page, which is acceptable for infinite scroll and APIs. Laravel provides `cursorPaginate()` for this. For exports and batch jobs, iterate by primary key in chunks instead of using `OFFSET`.

## Database Connections

Each PHP-FPM worker holds its own database connection, so the number of connections equals workers times servers: 20 servers with 100 workers each open 2,000 connections. PostgreSQL starts a process per connection and works best with a few hundred, and MySQL also degrades with thousands of threads.

- **Connection pooler:** PgBouncer for PostgreSQL and ProxySQL for MySQL let many client connections share a few dozen real ones. In PgBouncer's transaction mode, session state such as session variables, advisory locks and `LISTEN` does not work reliably.
- **Persistent connections** (`PDO::ATTR_PERSISTENT`) save the connection setup time, but the number of connections still equals the number of workers, and state such as an open transaction can leak into the next request.
- **Size pools together:** do not set `pm.max_children` higher than the database can serve.
- **Long-running runtimes** such as RoadRunner, Swoole and FrankenPHP can keep their own pools, but must reconnect after failures and reset state between requests.

## Queues and Traffic Spikes

A queue separates accepting work from processing it. During a spike, the web servers accept requests and publish messages, while workers consume them at the pace the database and external APIs can sustain.

- **At-least-once delivery:** most brokers can deliver a message more than once, so consumers must be idempotent, for example by deduplicating on a message ID or using a unique constraint.
- **Retries and a dead letter queue:** retry temporary errors with a growing delay, and move messages that keep failing to a separate queue.
- **Queue depth:** a growing queue means consumers cannot keep up. Add workers automatically based on queue depth.
- **Ordering:** parallel consumers do not preserve order. If order matters per entity, partition by entity ID, for example with Kafka partitions.
- **Limits:** set a maximum length or TTL and decide what happens when the queue is full.
- **Reliable publishing:** the Transactional Outbox pattern writes the message to a table in the same transaction, so it is not lost if the broker is unavailable.

## Preparing for a Traffic Spike

**Before the event:**

- Load test realistic scenarios at the expected peak and above to find the first bottleneck, usually the database.
- Cache static files and public pages in a CDN and hot data in Redis, and warm the caches before the start.
- Add servers in advance, because autoscaling takes minutes to react. Check other limits too: database connections, external API quotas and cloud account limits.
- Move non-critical work, such as emails and analytics, to queues.

**During the event:**

- Turn off expensive non-essential features, such as recommendations, with feature flags.
- Reject excess traffic early with rate limiting and HTTP 429 or 503 at the load balancer, instead of letting every request time out. A virtual waiting room is an option for extreme cases.
- Protect dependencies with timeouts and circuit breakers.
- Watch error rate, latency percentiles, queue depth and database load.

**After the event:** analyze what failed first and fix it before the next event.

## Zero-Downtime Deployments and Migrations

Deployment strategies:

- **Rolling:** replace instances one by one. Old and new versions run at the same time, so they must be compatible.
- **Blue-green:** deploy to an identical environment and switch all traffic at once. Rolling back means switching back.
- **Canary:** send a small share of traffic to the new version, watch the metrics and increase the share gradually.
- **Single server:** deploy into a new release directory, switch a `current` symlink atomically and reload PHP-FPM so OPcache picks up the new code.

Old code keeps running during a deploy, so migrations must be backward compatible. Use the expand and contract pattern, for example to rename a column:

1. **Expand:** add the new column, and make the code write to both columns.
2. **Migrate:** copy existing data in batches.
3. Switch reads to the new column.
4. **Contract:** in a later release, stop writing the old column and drop it.

Schema changes can lock large tables. In MySQL 8, use `ALGORITHM=INSTANT` or `INPLACE`, or online schema change tools such as gh-ost and pt-online-schema-change. In PostgreSQL, use `CREATE INDEX CONCURRENTLY` and set `lock_timeout` for migrations.

## Counters with Many Writes

`UPDATE posts SET views = views + 1 WHERE id = ?` on every request makes all transactions wait for the same row lock, and the database receives one write per view.

- **Aggregate in Redis:** run `INCR views:post:42` on each view, and let a scheduled job write the totals to the database every minute in one batch. The counter in the database is slightly behind, but the number of writes drops sharply.
- **Sharded counters:** split one counter into N rows (`post_id`, `shard_no`, `count`), update a random row and read the sum. Lock contention drops N times.
- **Event log:** write view events to a queue or Kafka and aggregate them in a consumer or an analytics database such as ClickHouse.
- **Unique visitors:** Redis HyperLogLog (`PFADD`, `PFCOUNT`) counts unique values in about 12 KB with about 0.81% error.

Likes must be unique per user and can be removed, so keep the `(user_id, post_id)` rows with a unique constraint as the source of truth and treat the displayed count as a cached value.

## Observability

Observability is the ability to understand what happens inside a system from its outputs:

- **Metrics:** numeric time series for dashboards and alerts, for example in Prometheus and Grafana.
- **Logs:** structured records with a request ID, collected in a system such as ELK or Loki.
- **Traces:** the path of one request across services with the time spent in each step, for example with OpenTelemetry and Jaeger.

Useful methods:

- **RED** for services: rate, errors and duration.
- **USE** for resources such as CPU, memory and connections: utilization, saturation and errors.
- **Four golden signals** from Google SRE: latency, traffic, errors and saturation.

Measure latency with percentiles (p50, p95, p99). An average hides the slow requests that real users notice. Define what you measure (SLI, for example the share of requests faster than 300 ms) and the target (SLO, for example 99.9% per month). Alert on symptoms users feel, not on every CPU spike.

## Load Testing

Common tools are k6, Gatling, JMeter and Locust for scenarios, and wrk2 for quick benchmarks of one endpoint.

Types of tests:

- **Load test:** expected peak traffic. Does the system meet its targets?
- **Stress test:** increase the load until the system fails. Where is the limit, and how does it fail?
- **Spike test:** a sudden jump in traffic. Do autoscaling and caches cope?
- **Soak test:** moderate load for hours. It reveals memory leaks, connection leaks and growing queues.

Practices:

- Test in a production-like environment with realistic data volumes. A query that is fast on 1,000 rows can be slow on 10 million.
- Model user scenarios such as browse, search, add to cart and check out, with pauses between steps.
- Watch p95 and p99 latency, error rate and throughput together with server saturation: CPU, the PHP-FPM `listen queue`, database connections and slow queries.
- Some tools wait for a slow response before sending the next request and underreport latency. k6 with arrival-rate executors and wrk2 avoid this.
- Change one thing at a time and compare the results.
