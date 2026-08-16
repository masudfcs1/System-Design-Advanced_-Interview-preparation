# Module 05 · Distributed Systems

**Goal:** Reason about coordination when processes, clocks, and networks are unreliable.  
**Suggested time:** 10–12 hours

## Learning outcomes

- [ ] Explain leader election and consensus at a practical level.
- [ ] Choose consistency, locking, transaction, discovery, and ID strategies.
- [ ] Reason about logical clocks and unreliable physical time.

## Topic map

| Area | Topics | Focus |
|---|---|---|
| Coordination | Leader election, consensus, Paxos, Raft | Quorums, terms/ballots, logs, safety vs liveness |
| Membership | Gossip protocol, service discovery | Failure suspicion and convergence |
| Cross-service state | Distributed locks, distributed transactions | Leases, fencing tokens, 2PC limitations |
| Consistency | Strong/eventual consistency, vector/Lamport clocks | Ordering, causality, conflict handling |
| Identity and time | Snowflake IDs, UUIDs, time synchronization | Uniqueness, ordering, hotspots, clock skew |

## বাংলা deep-learning prompt

একবারে একটি topic বসিয়ে prompt-টি কপি করুন: `Leader Election`, `Consensus`, `Paxos`, `Raft`, `Gossip Protocol`, `Service Discovery`, `Distributed Locks`, `Distributed Transactions`, `Eventual Consistency`, `Strong Consistency`, `Vector Clock`, `Lamport Clock`, `Snowflake ID`, `UUID`, `Time Synchronization`।

```text
আমি Senior/Staff Engineer এবং System Design interview-এর জন্য [TOPIC] শিখছি। Topic-টি basic থেকে advanced পর্যন্ত পরিষ্কার বাংলায় গভীরভাবে শেখান; English technical term পাশে রাখবেন।

উত্তরটি এই structure-এ দিন:
1. সহজ সংজ্ঞা, intuition ও কোন distributed problem এটি solve করে
2. assumptions, guarantees এবং যা guarantee করে না
3. algorithm/message flow step-by-step
4. একটি ছোট node-based example এবং একটি production/real-life example
5. quorum, failure detection, clock/ordering ও consistency details
6. competing approaches-এর comparison table
7. safety vs liveness, trade-offs, limitations, partitions ও failure modes
8. recovery, scaling, security ও observability considerations
9. pseudo-code বা sequence diagram (উপযোগী হলে)
10. Senior/Staff interview explanation, common mistakes ও follow-up questions
11. 5টি basic, 5টি intermediate ও 5টি advanced practice question copyable code block-এ দিন
12. concise answer key শেষে আলাদা section-এ দিন

Topic: [TOPIC]
Context: Distributed Systems
```

## Study path

1. Model crash, delay, duplication, reordering, partition, and clock-skew failures.
2. Walk through Raft leader election and replicated-log commitment.
3. Compare consensus, gossip, and a lease-based lock; do not treat them as interchangeable.
4. Use Lamport and vector clocks to order a small set of events.
5. Compare database-generated, UUID, and Snowflake-style IDs.

## Exercise

Design a distributed job scheduler that prevents concurrent ownership, survives worker and coordinator failures, and avoids a paused former leader performing stale writes.

## After-study checkpoint

1. What does a quorum intersection protect?
2. Why is a distributed lock unsafe without a lease and fencing token?
3. What can Lamport clocks tell you—and not tell you—about causality?
4. How does clock rollback affect a Snowflake-style ID generator?
5. Why is failure detection always based on suspicion in an asynchronous network?

**Complete when:** the scheduler handles partitions, stale leaders, duplicate execution, and recovery explicitly.

[← Caching](04-caching.md) · [Roadmap](../ROADMAP.md) · [Messaging →](06-messaging-systems.md)
