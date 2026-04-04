Q: What problem does a message queue solve?
A: Decouples producers from consumers — producers don't block waiting for slow consumers, consumers can process at their own rate, and the queue absorbs traffic spikes. Enables async processing and load leveling.

---

Q: What is the difference between a message queue and a message broker?
A: Message queue: point-to-point delivery, each message consumed by one consumer. Message broker (pub/sub): one message can be delivered to multiple subscribers. Kafka supports both; RabbitMQ supports both via exchanges.

---

Q: What is at-most-once, at-least-once, and exactly-once delivery?
A: At-most-once: messages may be lost, never duplicated (fire-and-forget). At-least-once: messages delivered at least once, may be duplicated — consumer must be idempotent. Exactly-once: no loss, no duplicates — expensive, requires distributed transactions or idempotent consumers + dedup.

---

Q: What is Kafka's architecture and what makes it different from traditional queues?
A: Kafka is a distributed commit log. Topics are split into partitions (ordered, append-only). Consumers track their own offset. Messages are retained for a configurable period regardless of consumption. Enables replay, multiple independent consumer groups, and massive throughput.

---

Q: What is a Kafka consumer group?
A: A set of consumers that jointly consume a topic — each partition is assigned to exactly one consumer in the group. Enables parallel consumption proportional to partition count without duplicate processing within the group.

---

Q: How do you guarantee ordering in Kafka?
A: Ordering is guaranteed only within a single partition. Route messages with the same key (e.g., user ID) to the same partition via key-based partitioning. Cross-partition ordering requires application-level sequencing.

---

Q: What is a dead letter queue (DLQ)?
A: A queue where messages are routed after repeated delivery failures. Prevents a bad message from blocking the main queue indefinitely. Enables investigation and reprocessing of failed messages.

---

Q: What is the outbox pattern?
A: Write a message to an "outbox" table in the same DB transaction as the business data change. A separate relay process reads the outbox and publishes to the message broker. Solves the dual-write problem — guarantees events are published if and only if the DB write succeeds.

---

Q: When would you use a task queue (Celery, Sidekiq) vs Kafka?
A: Task queue: job-oriented, retry logic built in, priority support, good for background jobs (emails, image processing). Kafka: high-throughput event streaming, replay, multiple consumers, audit log. Use Kafka when downstream consumers are independent or you need event sourcing.

---

Q: What is backpressure in the context of message queues?
A: When consumers can't keep up with producers, the queue grows unboundedly. Handle with: consumer auto-scaling, rate limiting producers, circuit breakers, or explicit consumer pause signals. Monitor consumer lag (offset lag in Kafka) as the primary signal.

---

C: The [saga pattern] uses a sequence of local transactions and compensating transactions to maintain data consistency across services — an alternative to distributed 2PC transactions over message queues.
