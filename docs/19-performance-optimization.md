# Module 19 · Performance Optimization

**Goal:** Improve performance from measurements while preserving correctness and stability.  
**Suggested time:** 6–8 hours

## Learning outcomes

- [ ] Profile latency, throughput, utilization, and queueing before optimizing.
- [ ] Tune pooling, pagination, compression, loading, and asynchronous work.
- [ ] Recognize when parallelism or prefetching creates overload.

## Topic map

| Area | Topics | Focus |
|---|---|---|
| Resource reuse | Connection pools, thread pools | Bounded concurrency, queueing, dependency limits |
| Data movement | Compression, pagination, cursor pagination, prefetching | CPU/network trade-off, stable traversal, wasted work |
| Execution | Lazy loading, async processing, parallel processing | Critical path, ordering, backpressure |
| Runtime | Memory optimization | Allocation, caching, leaks, garbage collection |

## বাংলা deep-learning prompt

একবারে একটি topic বসিয়ে prompt-টি কপি করুন: `Connection Pooling`, `Thread Pool`, `Compression`, `Lazy Loading`, `Pagination`, `Cursor Pagination`, `Prefetching`, `Async Processing`, `Parallel Processing`, `Memory Optimization`।

```text
আমি Senior Backend Engineer/System Design interview-এর জন্য [TOPIC] শিখছি। Topic-টি basic থেকে advanced পর্যন্ত পরিষ্কার বাংলায় গভীরভাবে শেখান; English technical term পাশে রাখবেন।

উত্তরটি এই structure-এ দিন:
1. সহজ সংজ্ঞা, mental model ও কোন bottleneck target করে
2. latency, throughput, utilization ও queueing-এর সঙ্গে সম্পর্ক
3. execution/resource flow step-by-step
4. একটি ছোট measured example এবং একটি production/real-life example
5. sizing, limits, tuning ও benchmarking details
6. relevant alternatives-এর comparison table
7. trade-offs, contention, overload, memory/CPU cost ও failure modes
8. backpressure, safety limits, observability ও rollback plan
9. optimization-এর আগে/পরে কোন metrics verify করব
10. Senior/Staff interview explanation, common mistakes ও follow-up questions
11. 5টি basic, 5টি intermediate ও 5টি advanced practice question copyable code block-এ দিন
12. concise answer key শেষে আলাদা section-এ দিন

Topic: [TOPIC]
Context: Performance Optimization
```

## Study path

1. Create a latency budget and measure percentiles, not only averages.
2. Apply Little’s Law conceptually to concurrency, throughput, and time in system.
3. Size pools against downstream capacity; keep queues bounded.
4. Compare offset and cursor pagination under concurrent inserts.
5. Optimize one bottleneck, then measure for regressions elsewhere.

## Exercise

Reduce a feed API’s p99 latency from 2 seconds to 300 milliseconds. Propose measurements, experiments, changes, and rollback criteria.

## After-study checkpoint

1. Why can a larger connection pool reduce throughput?
2. When does compression increase latency?
3. Why is cursor pagination more stable at scale?
4. How does unbounded asynchronous work become an outage?
5. What evidence proves an optimization helped users?

**Complete when:** each proposed change has a measured bottleneck, expected effect, safety limit, and verification method.

[← Data engineering](18-data-engineering.md) · [Roadmap](../ROADMAP.md) · [Interview problems →](20-interview-problems.md)
