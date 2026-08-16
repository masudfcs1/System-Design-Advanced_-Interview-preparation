# Module 06 · Messaging Systems

**Goal:** Decouple work while making delivery, ordering, and replay semantics explicit.  
**Suggested time:** 7–9 hours

## Learning outcomes

- [ ] Distinguish queues, publish/subscribe, and event streams.
- [ ] Select delivery and ordering guarantees for business requirements.
- [ ] Design retries, dead-letter handling, backpressure, and consumer scaling.

## Topic map

| Area | Topics | Focus |
|---|---|---|
| Models | Message queue, pub/sub, event streaming | Ownership, fan-out, retention, replay |
| Guarantees | At-most-once, at-least-once, “exactly once,” ordering | Define the boundary and duplicate behavior |
| Operations | Dead-letter queue, retry, lag, backpressure | Poison messages and controlled recovery |
| Products as examples | Kafka, RabbitMQ, ActiveMQ, Amazon SQS, Google Pub/Sub | Match product semantics to the workload |

## বাংলা deep-learning prompt

একবারে একটি topic বসিয়ে prompt-টি কপি করুন: `Kafka`, `RabbitMQ`, `ActiveMQ`, `Amazon SQS`, `Google Pub/Sub`, `Publish/Subscribe`, `Message Queue`, `Event Streaming`, `Dead-Letter Queue`, `Ordering`, `Exactly-Once`, `At-Least-Once`, `At-Most-Once`।

```text
আমি Senior Backend Engineer/System Design interview-এর জন্য [TOPIC] শিখছি। Topic-টি basic থেকে advanced পর্যন্ত পরিষ্কার বাংলায় গভীরভাবে শেখান; English technical term পাশে রাখবেন।

উত্তরটি এই structure-এ দিন:
1. সহজ সংজ্ঞা, mental model ও core use cases
2. producer, broker ও consumer architecture
3. publish, store, deliver, acknowledge ও retry flow step-by-step
4. একটি সহজ example এবং একটি production/real-life example
5. partitioning, ordering, retention, replay ও consumer scaling details
6. queue/pub-sub/stream বা relevant products-এর comparison table
7. delivery guarantees, duplicates, poison messages, backpressure ও failure modes
8. idempotency, schema evolution, recovery, security ও observability
9. capacity planning এবং 10× scale considerations
10. Senior/Staff interview explanation, common mistakes ও follow-up questions
11. 5টি basic, 5টি intermediate ও 5টি advanced practice question copyable code block-এ দিন
12. concise answer key শেষে আলাদা section-এ দিন

Topic: [TOPIC]
Context: Messaging Systems
```

## Study path

1. Trace producer → broker → consumer for each delivery guarantee.
2. Design idempotent consumers and a durable deduplication boundary.
3. Compare global ordering with partition/key ordering and its scaling cost.
4. Plan retry topics/queues, delay, attempt limits, and dead-letter replay.
5. Define metrics for producer failures, queue depth, lag, age, and consumer errors.

## Exercise

Design an order-event pipeline that fans out to payment, inventory, analytics, and notification consumers. Include schema evolution and poison-message handling.

## After-study checkpoint

1. Why is end-to-end exactly-once behavior difficult?
2. When does ordering reduce throughput?
3. What belongs in a dead-letter message?
4. How do queue depth and consumer lag differ?
5. When is a retained event log preferable to a work queue?

**Complete when:** the pipeline states delivery, ordering, retention, replay, and idempotency guarantees per consumer.

[← Distributed systems](05-distributed-systems.md) · [Roadmap](../ROADMAP.md) · [Event-driven architecture →](07-event-driven-architecture.md)
