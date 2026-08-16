# Module 01 · System Design Fundamentals

**Goal:** Build the vocabulary and decision framework used throughout the roadmap.  
**Suggested time:** 6–8 hours

## Learning outcomes

- [ ] Translate product requirements into measurable system qualities.
- [ ] Explain CAP, ACID/BASE, consistency, availability, latency, and throughput.
- [ ] Choose scaling and service-boundary approaches for a stated workload.
- [ ] Apply resilience patterns without causing retry storms or duplicate work.

## Topic map

| Area | Topics | Focus |
|---|---|---|
| Distributed trade-offs | CAP, consistency models, availability, partition tolerance | State what happens during a partition and what the user observes |
| Data semantics | ACID vs BASE, idempotency | Define invariants and safe repetition |
| Capacity | Latency vs throughput, horizontal vs vertical scaling | Identify the limiting resource and scaling unit |
| Service shape | Stateful vs stateless, monolith vs microservices, client–server | Prefer boundaries based on ownership and change patterns |
| Traffic management | API gateway, reverse proxy, L4/L7 load balancing, health checks | Know where routing, policy, and health decisions belong |
| Resilience | Rate limiting, circuit breaker, bulkhead, retry, timeout, backpressure | Bound work and prevent cascading failure |

## বাংলা deep-learning prompt

একবারে একটি topic বসিয়ে prompt-টি কপি করুন: `CAP Theorem`, `ACID vs BASE`, `Consistency Models`, `Availability`, `Partition Tolerance`, `Latency vs Throughput`, `Horizontal vs Vertical Scaling`, `Stateless vs Stateful Services`, `Monolith vs Microservices`, `Client–Server Architecture`, `API Gateway`, `Reverse Proxy`, `L4/L7 Load Balancer`, `Health Checks`, `Rate Limiting`, `Circuit Breaker`, `Bulkhead Pattern`, `Retry and Timeout`, `Idempotency`, `Backpressure`।

```text
আমি Senior Software Engineer/Staff Engineer/System Design interview-এর জন্য [TOPIC] শিখছি। আমাকে topic-টি basic থেকে advanced পর্যন্ত পরিষ্কার বাংলায় গভীরভাবে শেখান। Technical term-এর English নাম পাশে রাখবেন।

উত্তরটি এই structure-এ দিন:
1. সহজ সংজ্ঞা ও intuitive mental model
2. কেন প্রয়োজন এবং কখন ব্যবহার করা হয়
3. এটি কীভাবে কাজ করে—step-by-step flow
4. একটি সহজ example এবং একটি production/real-life example
5. গুরুত্বপূর্ণ variants, components ও implementation details
6. alternatives-এর সঙ্গে comparison table
7. trade-offs, limitations, bottlenecks, edge cases ও failure modes
8. scale, reliability, consistency, security ও observability impact
9. Senior/Staff-level interview-এ কীভাবে explain করব
10. common mistakes এবং follow-up questions
11. 5টি basic, 5টি intermediate ও 5টি advanced practice question আলাদা copyable code block-এ দিন
12. practice questions-এর concise answer key শেষে আলাদা section-এ দিন, যাতে আগে self-test করতে পারি

Topic: [TOPIC]
Context: System Design Fundamentals
```

## Study path

1. Write functional and non-functional requirements for a familiar product.
2. Compare strong, eventual, read-your-writes, and monotonic-read consistency.
3. Trace one request through DNS, gateway/proxy, load balancer, service, cache, and database.
4. Create a failure table for slow dependencies, overload, partial failure, and duplicate requests.
5. Explain why retries require deadlines, backoff, jitter, attempt limits, and idempotency.

## Exercise

Design the front door for a ticket-sale API that receives a 100× traffic spike. Include L4/L7 routing, health checks, per-user rate limits, timeouts, retry policy, circuit breaking, bulkheads, backpressure, and an idempotent purchase endpoint.

## After-study checkpoint

1. Why does CAP apply specifically when a network partition occurs?
2. When would vertical scaling be the best first move?
3. What is the difference between a reverse proxy, API gateway, and load balancer?
4. How can retries amplify an outage?
5. What state must an idempotency implementation persist, and for how long?

**Complete when:** you can defend the ticket API under overload and explain every resilience control’s boundary.

[← How to study](00-how-to-study.md) · [Roadmap](../ROADMAP.md) · [Networking →](02-networking.md)
