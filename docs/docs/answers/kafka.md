---
title: 'Kafka: High-Performance Distributed Streaming'
slug: '/answers/kafka'
---

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

### 1. Topic Partitioning (Divider)
By using keys, you can ensure that messages related to the same entity (e.g., `user_id`) always go to the same partition.

```php
<?php

declare(strict_types=1);

use RdKafka\Conf;
use RdKafka\Producer;

$conf = new Conf();
$conf->set('metadata.broker.list', 'localhost:9092');

$producer = new Producer($conf);
$topic = $producer->newTopic("orders-topic");

// PHP 8.4: Using keys to ensure order within a partition
$orderId = "order_12345";
$payload = json_encode(['status' => 'created', 'id' => $orderId]);

// The key 'order_12345' ensures all messages for this order go to the same partition
$topic->produce(RD_KAFKA_PARTITION_UA, 0, $payload, $orderId);

$producer->flush(10000);
```

### 2. Multi-Consumer Strategy (Consumer Groups)
To consume a topic with multiple consumers where each message is delivered to only **one** consumer in the group.

```php
<?php

declare(strict_types=1);

use RdKafka\Conf;
use RdKafka\KafkaConsumer;

$conf = new Conf();
$conf->set('group.id', 'inventory-service-group'); // Multiple instances use the same ID
$conf->set('metadata.broker.list', 'localhost:9092');
$conf->set('auto.offset.reset', 'earliest');

$consumer = new KafkaConsumer($conf);
$consumer->subscribe(['orders-topic']);

while (true) {
    $message = $consumer->consume(120 * 1000);
    switch ($message->err) {
        case RD_KAFKA_RESP_ERR_NO_ERROR:
            // Process message once across the whole group
            echo "Processing order: " . $message->payload . PHP_EOL;
            $consumer->commitAsync($message);
            break;
        case RD_KAFKA_RESP_ERR__PARTITION_EOF:
            break;
        case RD_KAFKA_RESP_ERR__TIMED_OUT:
            break;
        default:
            throw new \Exception($message->errstr(), $message->err);
    }
}
```

### 3. Preventing Duplicates & Idempotency
To avoid duplicates during retries or failures, we enable producer idempotency and handle offsets manually in the consumer.

**Producer (Idempotency):**
```php
$conf->set('enable.idempotence', 'true');
$conf->set('acks', 'all');
```

**Consumer (Retry Strategy with Idempotency):**
If a consumer fails after processing but before committing, it will receive the message again. The application must be idempotent.

```php
// PHP 8.4 Example: Idempotent processing with database check
public function processMessage(object $message): void
{
    $data = json_decode($message->payload, true);
    $messageId = $data['id'] ?? null;

    // Check if we already processed this message
    if ($this->db->exists('processed_messages', ['msg_id' => $messageId])) {
        return; // Already processed, skip
    }

    $this->db->transactional(function() use ($data, $messageId) {
        $this->doWork($data);
        $this->db->insert('processed_messages', ['msg_id' => $messageId]);
    });
}
```

### 4. Handling Race Conditions (Repeat Strategies)
When multiple workers might try to process the same "state" (e.g., updating a balance), use **Optimistic Locking** or **Atomic Transactions**.

```php
// Using a version column for optimistic locking in PHP 8.4
try {
    $this->db->execute(
        "UPDATE accounts SET balance = balance - :amount, version = version + 1 
         WHERE id = :id AND version = :current_version",
        ['amount' => 100, 'id' => $accountId, 'current_version' => $data['version']]
    );
} catch (LockException $e) {
    // Retry strategy: fetch latest state and try again, or push to a retry topic
    $this->retryTopic->produce(RD_KAFKA_PARTITION_UA, 0, $message->payload);
}
```
