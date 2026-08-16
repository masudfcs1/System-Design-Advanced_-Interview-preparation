# Module 10 · Scaling

**Goal:** Scale each bottleneck deliberately, including across regions.  
**Suggested time:** 6–8 hours

## Learning outcomes

- [ ] Select replication, partitioning, sharding, autoscaling, and load balancing strategies.
- [ ] Identify hot partitions and unsafe autoscaling feedback loops.
- [ ] Explain multi-region consistency, routing, and failover trade-offs.

## Topic map

| Area | Topics | Focus |
|---|---|---|
| Data | Read replicas, replication, partitioning, database sharding | Routing, lag, rebalancing, hot keys |
| Compute | Autoscaling, load balancing | Signals, warm-up, draining, state |
| Geography | Geo-replication, multi-region deployment | Latency, sovereignty, consistency, recovery |

## বাংলা deep-learning prompt

একবারে একটি topic বসিয়ে prompt-টি কপি করুন: `Read Replica`, `Database Sharding`, `Partitioning`, `Replication`, `Autoscaling`, `Load Balancing`, `Geo-Replication`, `Multi-Region Deployment`।

```text
আমি Senior/Staff Engineer/System Design interview-এর জন্য [TOPIC] শিখছি। Topic-টি basic থেকে advanced পর্যন্ত পরিষ্কার বাংলায় গভীরভাবে শেখান; English technical term পাশে রাখবেন।

উত্তরটি এই structure-এ দিন:
1. সহজ সংজ্ঞা, mental model ও কোন bottleneck এটি solve করে
2. prerequisites, signals এবং scaling unit
3. routing/rebalancing/failover flow step-by-step
4. একটি ছোট example এবং একটি production/real-life example
5. key/partition selection, replication lag, capacity ও migration details
6. vertical/horizontal এবং relevant alternatives-এর comparison table
7. hot spots, feedback loops, consistency, cost ও failure modes
8. recovery, security, observability ও operational safeguards
9. 10×, 100× ও multi-region scale-এ design evolution
10. Senior/Staff interview explanation, common mistakes ও follow-up questions
11. 5টি basic, 5টি intermediate ও 5টি advanced practice question copyable code block-এ দিন
12. concise answer key শেষে আলাদা section-এ দিন

Topic: [TOPIC]
Context: Scaling
```

## Study path

1. Start with one machine and identify the first resource to saturate.
2. Add read replicas and state how clients handle replication lag.
3. Choose and challenge a sharding key; plan resharding.
4. Define autoscaling signals, limits, stabilization, and dependency capacity.
5. Compare single-region, active-passive, and active-active deployments.

## Exercise

Evolve a single-region social feed from 10,000 to 100 million users. Show which change happens at each bottleneck and why.

## After-study checkpoint

1. When can a read replica return an impossible user experience?
2. Why is rebalancing often harder than initial sharding?
3. What happens if compute autoscales faster than the database can handle?
4. How do you route users during a regional failure?
5. Which writes are difficult in multi-region active-active systems?

**Complete when:** every scaling step solves a named bottleneck and states its new failure modes.

[← Search](09-search-systems.md) · [Roadmap](../ROADMAP.md) · [Reliability →](11-reliability.md)
