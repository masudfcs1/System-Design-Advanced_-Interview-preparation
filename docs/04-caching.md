# Module 04 · Caching

**Goal:** Improve latency and capacity without losing control of freshness and failure behavior.  
**Suggested time:** 5–7 hours

## Learning outcomes

- [ ] Choose browser, CDN, local, or distributed caching by scope.
- [ ] Apply cache-aside, read-through, write-through, write-around, and write-back.
- [ ] Design invalidation and mitigate stampede, avalanche, and penetration.

## Topic map

| Area | Topics | Focus |
|---|---|---|
| Placement | Browser cache, CDN cache, local cache, distributed cache | Ownership, reach, latency, consistency |
| Products | Redis, Memcached | Data structures, persistence needs, operational cost |
| Strategies | Cache-aside, read-through, write-through, write-around, write-back | Source of truth and write failure semantics |
| Failure modes | Stampede, avalanche, penetration, stale data | Coalescing, jitter, negative caching, admission controls |
| Lifecycle | Keys, TTLs, eviction, invalidation | Versioning and bounded staleness |

## বাংলা deep-learning prompt

একবারে একটি topic বসিয়ে prompt-টি কপি করুন: `Browser Cache`, `CDN Cache`, `Redis`, `Memcached`, `Local Cache`, `Distributed Cache`, `Cache-Aside`, `Read-Through`, `Write-Through`, `Write-Around`, `Write-Back`, `Cache Stampede`, `Cache Avalanche`, `Cache Penetration`, `Cache Invalidation`।

```text
আমি Senior Backend Engineer/System Design interview-এর জন্য [TOPIC] শিখছি। Topic-টি basic থেকে advanced পর্যন্ত পরিষ্কার বাংলায় শেখান; English technical term বজায় রাখবেন।

উত্তরটি এই structure-এ দিন:
1. সহজ সংজ্ঞা ও mental model
2. কেন/কখন ব্যবহার করা হয় এবং source of truth কোথায় থাকে
3. cache hit, miss, read ও write flow step-by-step
4. একটি সহজ example এবং একটি production/real-life example
5. key design, TTL, eviction, invalidation ও consistency details
6. alternatives/strategies-এর comparison table
7. trade-offs, stale data, hot key, memory pressure ও failure modes
8. stampede/avalanche/penetration mitigation এবং cache outage behavior
9. scale, reliability, security ও observability considerations
10. Senior/Staff interview explanation, common mistakes ও follow-up questions
11. 5টি basic, 5টি intermediate ও 5টি advanced practice question copyable code block-এ দিন
12. concise answer key শেষে আলাদা section-এ দিন

Topic: [TOPIC]
Context: Caching
```

## Study path

1. Define cache keys, values, ownership, TTL, and invalidation for three workloads.
2. Trace hit, miss, stale read, cache outage, and database outage paths.
3. Compare consistency behavior for every read/write strategy.
4. Study hot keys and coordinated expiry; add request coalescing and TTL jitter.
5. Treat the cache as unavailable and verify the origin remains protected.

## Exercise

Add multi-layer caching to a product catalog with price updates, regional inventory, and flash-sale traffic. State what may be stale and for how long.

## After-study checkpoint

1. When does a local cache become dangerous?
2. How do write-through and write-back differ during failure?
3. Why can synchronized TTLs cause a cache avalanche?
4. What prevents cache penetration for nonexistent keys?
5. How would you invalidate millions of dependent keys?

**Complete when:** the catalog design defines freshness, invalidation, hot-key protection, and cache-outage behavior.

[← Databases](03-databases.md) · [Roadmap](../ROADMAP.md) · [Distributed systems →](05-distributed-systems.md)
