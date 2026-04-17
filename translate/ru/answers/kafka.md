# Kafka: высокопроизводительный распределенный стриминг

Apache Kafka — это распределенное хранилище событий и платформа для потоковой обработки данных. Она разработана для обработки огромных объемов данных в реальном времени с низкой задержкой и высокой пропускной способностью.

## 1. Основные концепции и EOS
### Семантика Exactly-Once (EOS)
Kafka обеспечивает семантику «ровно один раз» (exactly-once) с помощью:
- **Идемпотентные продюсеры**: Гарантируют, что повторные попытки отправки не приведут к дублированию сообщений за счет присвоения порядкового номера каждому сообщению.
- **Транзакции**: Позволяют объединять несколько операций Kafka (в разных разделах/топиках) в единую атомарную единицу.
- **Управление смещениями (offsets)**: При использовании Kafka Streams или транзакционных потребителей смещения фиксируются атомарно вместе с результатами обработки.

**Проблемы:**
- Требуется идемпотентность на стороне потребителя (обработка одного и того же сообщения дважды, если фиксация смещения не удалась).
- Продюсеры должны включить `acks=all` и `enable.idempotence=true`.
- Увеличение задержки из-за накладных расходов на транзакционные записи и координацию.

## 2. High-Water Mark (HW)
- **High-Water Mark (HW)** — это последнее смещение, которое считается «зафиксированным» (committed) и может быть прочитано потребителями.
- Это обеспечивает согласованность: потребители не могут читать незафиксированные данные, которые могут исчезнуть в случае сбоя лидера.
- Если реплика-последователь (follower) отстает от HW, она не может быть выбрана лидером до тех пор, пока не догонит его.

## 3. Сбой лидера и сообщения в полете
- Kafka использует **ZooKeeper** или **KRaft** для выбора нового лидера из **In-Sync Replicas (ISR)**.
- **In-flight сообщения** (с `acks=1` или `acks=0`) могут быть потеряны, если они не были реплицированы на последователя до падения лидера.
- С `acks=all` сообщение считается успешно отправленным только если оно было реплицировано на все ISR.
- **Крайний случай**: Если нет доступных ISR, Kafka может приостановить запись, чтобы предотвратить потерю данных (в зависимости от настройки `min.insync.replicas`).

## 4. Log Compaction vs. Retention
- **Log Compaction (Уплотнение логов)**: Сохраняет как минимум последнюю версию для каждого ключа. Старые версии удаляются в процессе уплотнения. Полезно для хранения текущего состояния (changelogs).
- **Retention Policies (Политики удержания)**: Удаляют сообщения на основе времени (`log.retention.ms`) или общего размера (`log.retention.bytes`), независимо от ключа.
- **Сложный случай**: Если потребитель был отключен дольше периода удержания, он может пропустить исторические обновления или изменения состояния.

## 5. Нечистый выбор лидера (Unclean Leader Election)
- Когда `unclean.leader.election.enable=true`, последователь, не находящийся в синхронизации (не в ISR), может стать лидером.
- **Опасность**: Это может привести к потере данных и несогласованности, так как новый лидер может не иметь всех зафиксированных сообщений от предыдущего лидера.
- В продакшене это значение обычно должно быть `false`, если только доступность не важнее согласованности.

## 6. Предотвращение дубликатов (Идемпотентность)
- Каждое сообщение имеет **Producer ID (PID)** и **Sequence Number**.
- Брокер отслеживает последний подтвержденный порядковый номер для каждого PID.
- Если продюсер повторно отправляет сообщение (например, из-за таймаута сети), брокер отбрасывает его как дубликат.

## 7. Backpressure и отставание (Lag)
- **Consumer Lag (Отставание потребителя)**: Возникает, когда скорость продюсера превышает скорость обработки потребителем.
- **Обработка**:
  - Используйте методы `pause()` и `resume()` в API потребителя.
  - Масштабируйтесь горизонтально, добавляя больше потребителей в **Consumer Group**.
  - Оптимизируйте логику обработки или используйте пакетную обработку (batching).
  - Внедрите ограничение скорости (rate limiting) на стороне продюсера.

## 8. ISR, OSR и недореплицированные разделы
- **ISR (In-Sync Replicas)**: Последователи, которые полностью синхронизированы с лидером.
- **OSR (Out-of-Sync Replicas)**: Последователи, которые отстают.
- **Under-Replicated Partitions (URP)**: Разделы, в которых количество ISR меньше заданного фактора репликации.

## 9. Kafka Streams и обработка состояний
- Использует **RocksDB** для эффективного локального хранения состояния.
- **Changelog Topics**: Каждое изменение состояния записывается в топик Kafka, что позволяет восстановить состояние после сбоя.
- **Punctuation**: Позволяет выполнять периодические задачи (например, оконные функции) без повторного чтения всех данных.

## 10. Мониторинг отставания потребителя
- Используйте CLI `kafka-consumer-groups.sh`.
- Мониторьте метрики JMX, такие как `records-lag-max`.
- Используйте инструменты вроде **Prometheus**, **Grafana** или **Burrow** для оповещений.

---

## Примеры реализации на PHP 8.4

Для работы с Kafka в PHP стандартом де-факто является расширение `php-rdkafka`.

### 1. Разделение топика (Divider/Partitioner) — Глубокое погружение
Kafka использует партиционер для принятия решения, в какой раздел отправится сообщение. По умолчанию это `hash(key) % num_partitions`. Использование консистентного ключа гарантирует **глобальный порядок** для всех сообщений с этим ключом.

```php
<?php

declare(strict_types=1);

use RdKafka\Conf;
use RdKafka\Producer;

$conf = new Conf();
// Настройки высокой доступности
$conf->set('metadata.broker.list', 'kafka1:9092,kafka2:9092');
$conf->set('acks', 'all'); // Ждем подтверждения от всех ISR
$conf->set('compression.codec', 'snappy'); // Эффективность при высокой нагрузке

$producer = new Producer($conf);
$topic = $producer->newTopic("order_events");

/**
 * PHP 8.4: Стратегия обеспечения привязки к шарду/разделу (affinity).
 * Мы используем 'user_id' в качестве ключа. Все заказы для Пользователя X попадут 
 * в один и тот же раздел, что позволит обрабатывать события этого пользователя 
 * строго последовательно в потребителе.
 */
function produceUserEvent(Producer $producer, string $topicName, string $userId, array $data): void {
    $topic = $producer->newTopic($topicName);
    $payload = json_encode([
        'event_id' => bin2hex(random_bytes(16)),
        'timestamp' => time(),
        ...$data
    ]);

    // RD_KAFKA_PARTITION_UA сообщает Kafka использовать внутреннюю логику партиционера (хеш Murmur2 от ключа)
    $topic->produce(RD_KAFKA_PARTITION_UA, 0, $payload, $userId);
}

produceUserEvent($producer, "order_events", "user_789", ['action' => 'order_placed', 'amount' => 150.00]);
$producer->flush(5000);
```

### 2. Стратегия с несколькими потребителями (Масштабирование и Ребаланс)
В Consumer Group Kafka распределяет разделы между потребителями. Если у вас 10 разделов и 2 потребителя, каждый получит по 5. Если один упадет, оставшийся получит все 10 (**Ребалансировка**).

```php
<?php

declare(strict_types=1);

use RdKafka\Conf;
use RdKafka\KafkaConsumer;

$conf = new Conf();
$conf->set('group.id', 'payment-worker-group'); // Все инстансы в этой группе делят нагрузку
$conf->set('metadata.broker.list', 'localhost:9092');
$conf->set('auto.offset.reset', 'earliest');
$conf->set('enable.auto.commit', 'false'); // Будем коммитить вручную после обработки

$consumer = new KafkaConsumer($conf);
$consumer->subscribe(['order_events']);

echo "Ожидание сообщений... (Группа: payment-worker-group)\n";

while (true) {
    $message = $consumer->consume(1000); // Таймаут 1 секунда
    
    if ($message->err === RD_KAFKA_RESP_ERR_NO_ERROR) {
        // Логика высокопроизводительной обработки
        try {
            processOrder(json_decode($message->payload, true));
            // Синхронный коммит смещений для гарантии, что мы не пропустим сообщения при ошибке
            $consumer->commit($message); 
        } catch (\Throwable $e) {
            // Логируем ошибку и, возможно, отправляем в Dead Letter Queue (DLQ)
            handleFailure($message);
        }
    }
}
```

### 3. Транзакции и идемпотентность продюсера
Для достижения семантики **Exactly-Once** мы используем транзакционные продюсеры. Это гарантирует, что набор сообщений будет либо полностью записан, либо не записан вовсе.

```php
/**
 * PHP 8.4: Поток транзакционного продюсера
 * Полезно при схеме: чтение из Топика А -> обработка -> запись в Топик Б
 */
$conf->set('transactional.id', 'order_processor_tx_1');
$conf->set('enable.idempotence', 'true');

$producer = new Producer($conf);
$producer->initTransactions(10000);

try {
    $producer->beginTransaction();
    
    // Шаг 1: Обновление топика инвентаризации
    $producer->newTopic("inventory_updates")->produce(RD_KAFKA_PARTITION_UA, 0, "Item_123:Decr:1");
    
    // Шаг 2: Событие в топик биллинга
    $producer->newTopic("billing_events")->produce(RD_KAFKA_PARTITION_UA, 0, "Charge:User_456:Amount:50");
    
    $producer->commitTransaction(10000);
} catch (\Throwable $e) {
    $producer->abortTransaction(10000);
    throw $e;
}
```

### 4. Сложное состояние гонки (Saga / Оптимистичная блокировка с State Machine)
При обработке финансовых транзакций простого `UPDATE` недостаточно. Нам нужно обрабатывать конкурентные обновления и потенциальные сбои в распределенной среде.

```php
/**
 * Сценарий: Конкурентное списание баланса.
 * Проблема: Два воркера прочитали Баланс=100. Оба пытаются списать 60.
 * Результат: Без обработки баланс станет -20 (ошибка) или один воркер перезапишет другого.
 */
function handleBalanceDeduction(Database $db, array $event): void {
    $userId = $event['user_id'];
    $amountToDeduct = $event['amount'];

    // PHP 8.4: Оптимистичная блокировка с версионированием И проверкой статуса
    $db->transactional(function() use ($db, $userId, $amountToDeduct) {
        $wallet = $db->fetch("SELECT balance, version, status FROM wallets WHERE user_id = ?", [$userId]);
        
        if ($wallet['status'] !== 'active') {
            throw new AccountLockedException("Кошелек заблокирован");
        }

        if ($wallet['balance'] < $amountToDeduct) {
            // Вместо простого падения можно отправить событие в топик 'insufficient_funds'
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
            // RACECONDITION: Кто-то другой обновил баланс между нашим SELECT и UPDATE
            // Стратегия: Бросаем исключение для повторной попытки (RdKafka retry)
            throw new ConcurrencyConflictException("Баланс обновлен другим воркером");
        }
        
        // Успех: логируем транзакцию локально для аудита
        $db->insert('wallet_transactions', ['user_id' => $userId, 'amount' => -$amountToDeduct]);
    });
}
```

### 5. Распределенное логирование с Kafka
Kafka — отличный буфер для логов (стек ELK/Graylog). Она предотвращает падение приложения, если хранилище логов (например, Elasticsearch) работает медленно или недоступно.

```php
/**
 * PHP 8.4: Реализация Kafka Logger (упрощенная логика Monolog)
 */
class KafkaLogger {
    private Producer $producer;

    public function __construct(string $brokers) {
        $conf = new Conf();
        $conf->set('metadata.broker.list', $brokers);
        $conf->set('queue.buffering.max.ms', '50'); // Отправка логов пачками каждые 50мс
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

        // Использование уровня лога в качестве ключа поможет сгруппировать логи в разделах
        $this->producer->newTopic("app_logs")->produce(RD_KAFKA_PARTITION_UA, 0, $payload, $level);
        $this->producer->poll(0); // Асинхронный вызов внутренних коллбэков
    }
}

$logger = new KafkaLogger("kafka-broker:9092");
$logger->log("ERROR", "Платеж не прошел для User #123", ['error' => 'Connection timeout']);
```
