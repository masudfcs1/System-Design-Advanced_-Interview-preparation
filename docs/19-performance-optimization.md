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

