# Kafka: High-Performance Distributed Streaming

Apache Kafka is a distributed event store and stream-processing platform. It is designed to handle high-volume, real-time data feeds with low latency and high throughput.

## 1. Core Concepts & EOS
### Exactly-Once Processing (EOS)
Kafka ensures exactly-once semantics (EOS) using:
- **Idempotent Producers**: Ensures that retries do not lead to duplicate messages by assigning a sequence number to each message.
- **Transactions**: Allows grouping multiple Kafka operations (across different partitions/topics) into a single atomic unit.
- **Consumer Offset Management**: When using Kafka Streams or transactional consumers, offsets are committed atomically along with the processing results.

**Challenges:**
- Requires idempotency on the consumer side (handling the same message twice if the commit fails).
- Producers must enable `acks=all` and `enable.idempotence=true`.
- Increased latency due to the overhead of transactional writes and coordination.

## 2. High-Water Mark (HW)
- **High-Water Mark (HW)** is the last offset that is considered “committed” and can be read by consumers.
- It ensures consistency: consumers cannot read uncommitted data that might disappear if the leader fails.
- If a follower lags behind the HW, it won't be promoted to a leader until it catches up.

## 3. Leader Failure & In-flight Messages
- Kafka uses **ZooKeeper** or **KRaft** to elect a new leader from the **In-Sync Replicas (ISR)**.
- **In-flight messages** (with `acks=1` or `acks=0`) may be lost if they weren't replicated to the follower before the leader crashed.
- With `acks=all`, a message is only considered successful if it was replicated to all ISRs.
- **Edge Case**: If no ISRs are available, Kafka might pause writes to prevent data loss (depending on `min.insync.replicas`).

## 4. Log Compaction vs. Retention
- **Log Compaction**: Retains at least the latest version of each key. Older versions are deleted during the compaction process. Useful for changelogs.
- **Retention Policies**: Deletes messages based on age (`log.retention.ms`) or total size (`log.retention.bytes`), regardless of the key.
- **Tricky Case**: If a consumer is down longer than the retention period, it may miss historical updates or state changes.

## 5. Unclean Leader Election
- When `unclean.leader.election.enable=true`, an out-of-sync follower (not in ISR) can become the leader.
- **Danger**: This can lead to data loss and inconsistencies, as the new leader might not have all the committed messages from the previous leader.
- In production, it should usually be `false` unless availability is preferred over consistency.

## 6. Duplicate Prevention (Idempotency)
- Each message has a **Producer ID (PID)** and a **Sequence Number**.
- The broker tracks the last acknowledged sequence number for each PID.
- If a producer resends a message (e.g., due to a network timeout), the broker discards it as a duplicate.

## 7. Backpressure & Lag
- **Consumer Lag**: Occurs when the producer rate exceeds the consumer processing rate.
- **Handling**:
  - Use `pause()` and `resume()` APIs in the consumer.
  - Scale horizontally by adding more consumers to the **Consumer Group**.
  - Optimize processing logic or use batch processing.
  - Implement rate limiting on the producer side.

## 8. ISR, OSR, and Under-Replicated Partitions
- **ISR (In-Sync Replicas)**: Followers that are up-to-date with the leader.
- **OSR (Out-of-Sync Replicas)**: Followers that are lagging behind.
- **Under-Replicated Partitions (URP)**: Partitions where the number of ISRs is less than the requested replication factor.

## 9. Kafka Streams & Stateful Processing
- Uses **RocksDB** for efficient local state storage.
- **Changelog Topics**: Every state change is backed by a Kafka topic, allowing state recovery after a crash.
- **Punctuation**: Allows for periodic processing tasks (e.g., windowing) without re-reading all data.

## 10. Monitoring Consumer Lag
- Use `kafka-consumer-groups.sh` CLI.
- Monitor JMX metrics like `records-lag-max`.
- Use tools like **Prometheus**, **Grafana**, or **Burrow** for alerting.

---

## PHP 8.4 Implementation Examples

To interact with Kafka in PHP, the `php-rdkafka` extension is the industry standard.

### 1. Topic Partitioning (Divider) - Deep Dive
Kafka uses a partitioner to decide which partition a message goes to. By default, it's `hash(key) % num_partitions`. Using a consistent key ensures **global order** for messages with that same key.

```php
<?php

declare(strict_types=1);

use RdKafka\Conf;
use RdKafka\Producer;

$conf = new Conf();
// High availability settings
$conf->set('metadata.broker.list', 'kafka1:9092,kafka2:9092');
$conf->set('acks', 'all'); // Wait for all ISRs to acknowledge
$conf->set('compression.codec', 'snappy'); // Efficiency for highload

$producer = new Producer($conf);
$topic = $producer->newTopic("order_events");

/**
 * PHP 8.4: Strategy to ensure specific shard/partition affinity.
 * We use 'user_id' as a key. All orders for User X will land in the same partition,
 * allowing us to process User X's events sequentially in the consumer.
 */
function produceUserEvent(Producer $producer, string $topicName, string $userId, array $data): void {
    $topic = $producer->newTopic($topicName);
    $payload = json_encode([
        'event_id' => bin2hex(random_bytes(16)),
        'timestamp' => time(),
        ...$data
    ]);

    // RD_KAFKA_PARTITION_UA tells Kafka to use its internal partitioner logic (Murmur2 hash of key)
    $topic->produce(RD_KAFKA_PARTITION_UA, 0, $payload, $userId);
}

produceUserEvent($producer, "order_events", "user_789", ['action' => 'order_placed', 'amount' => 150.00]);
$producer->flush(5000);
```

### 2. Multi-Consumer Strategy (Scaling & Rebalancing)
In a Consumer Group, Kafka distributes partitions among consumers. If you have 10 partitions and 2 consumers, each gets 5. If one fails, the remaining gets all 10 (**Rebalance**).

```php
<?php

declare(strict_types=1);

use RdKafka\Conf;
use RdKafka\KafkaConsumer;

$conf = new Conf();
$conf->set('group.id', 'payment-worker-group'); // All instances in this group share the load
$conf->set('metadata.broker.list', 'localhost:9092');
$conf->set('auto.offset.reset', 'earliest');
$conf->set('enable.auto.commit', 'false'); // We will commit manually after processing

$consumer = new KafkaConsumer($conf);
$consumer->subscribe(['order_events']);

echo "Waiting for messages... (Consumer Group: payment-worker-group)\n";

while (true) {
    $message = $consumer->consume(1000); // 1s timeout
    
    if ($message->err === RD_KAFKA_RESP_ERR_NO_ERROR) {
        // High-performance processing logic
        try {
            processOrder(json_decode($message->payload, true));
            // Commit offsets synchronously to ensure we don't skip messages on error
            $consumer->commit($message); 
        } catch (\Throwable $e) {
            // Log error and potentially move to a Dead Letter Queue (DLQ)
            handleFailure($message);
        }
    }
}
```

### 3. Transactions & Producer Idempotency
To achieve **Exactly-Once**, we use transactional producers. This ensures that a set of messages is either all committed or none.

```php
/**
 * PHP 8.4: Transactional Producer Flow
 * Useful when consuming from Topic A and producing to Topic B (Process-Transform-Sink)
 */
$conf->set('transactional.id', 'order_processor_tx_1');
$conf->set('enable.idempotence', 'true');

$producer = new Producer($conf);
$producer->initTransactions(10000);

try {
    $producer->beginTransaction();
    
    // Step 1: Update Inventory Topic
    $producer->newTopic("inventory_updates")->produce(RD_KAFKA_PARTITION_UA, 0, "Item_123:Decr:1");
    
    // Step 2: Notify Billing Topic
    $producer->newTopic("billing_events")->produce(RD_KAFKA_PARTITION_UA, 0, "Charge:User_456:Amount:50");
    
    $producer->commitTransaction(10000);
} catch (\Throwable $e) {
    $producer->abortTransaction(10000);
    throw $e;
}
```

### 4. Complex Race Condition (Saga / Optimistic Locking with State Machine)
When processing financial transactions, a simple `UPDATE` isn't enough. We need to handle concurrent updates and potential failures across distributed workers.

```php
/**
 * Scenario: Concurrent balance deduction.
 * Problem: Two workers read Balance=100. Both try to deduct 60.
 * Result: If not handled, balance becomes -20 (invalid) or one overwrite another.
 */
function handleBalanceDeduction(Database $db, array $event): void {
    $userId = $event['user_id'];
    $amountToDeduct = $event['amount'];

    // PHP 8.4: Using Optimistic Locking with versioning AND State check
    $db->transactional(function() use ($db, $userId, $amountToDeduct) {
        $wallet = $db->fetch("SELECT balance, version, status FROM wallets WHERE user_id = ?", [$userId]);
        
        if ($wallet['status'] !== 'active') {
            throw new AccountLockedException("Wallet is not active");
        }

        if ($wallet['balance'] < $amountToDeduct) {
            // Instead of just failing, we could push to a 'insufficient_funds' topic
            throw new InsufficientFundsException();
        }

        $affectedRows = $db->execute(
            "UPDATE wallets 
             SET balance = balance - :deduct, 
                 version = version + 1, 
                 last_updated = NOW()
             WHERE user_id = :uid 
               AND version = :v 
               AND balance >= :deduct", 
            [
                'deduct' => $amountToDeduct,
                'uid' => $userId,
                'v' => $wallet['version']
            ]
        );

        if ($affectedRows === 0) {
            // RACECONDITION: Someone else updated the wallet between our SELECT and UPDATE
            // Strategy: Throw exception to trigger a retry at the consumer level (RdKafka retry)
            throw new ConcurrencyConflictException("Balance updated by another worker");
        }
        
        // Success: Log the transaction locally for auditing
        $db->insert('wallet_transactions', ['user_id' => $userId, 'amount' => -$amountToDeduct]);
    });
}
```

### 5. Distributed Logging with Kafka
Kafka is an excellent buffer for high-volume logs (ELK/Graylog stack). It prevents your app from crashing if the logging backend (Elasticsearch) is slow.

```php
/**
 * PHP 8.4: Kafka Logger implementation (simplified Monolog logic)
 */
class KafkaLogger {
    private Producer $producer;

    public function __construct(string $brokers) {
        $conf = new Conf();
        $conf->set('metadata.broker.list', $brokers);
        $conf->set('queue.buffering.max.ms', '50'); // Send logs in small batches every 50ms
        $this->producer = new Producer($conf);
    }

    public function log(string $level, string $message, array $context = []): void {
        $payload = json_encode([
            '@timestamp' => gmdate('c'),
            'level' => $level,
            'message' => $message,
            'context' => $context,
            'server' => gethostname()
        ]);

        // Using level as key can help group logs in partitions (e.g., all 'ERROR' in one partition)
        $this->producer->newTopic("app_logs")->produce(RD_KAFKA_PARTITION_UA, 0, $payload, $level);
        $this->producer->poll(0); // Non-blocking trigger for internal callbacks
    }
}

$logger = new KafkaLogger("kafka-broker:9092");
$logger->log("ERROR", "Payment failed for User #123", ['error' => 'Connection timeout']);
```
