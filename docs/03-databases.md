# Module 03 · Databases

**Goal:** Select and shape a database around invariants and access patterns.  
**Suggested time:** 10–12 hours

## Learning outcomes

- [ ] Model data from access patterns and consistency requirements.
- [ ] Explain indexes, query plans, transactions, isolation, locking, and deadlocks.
- [ ] Distinguish replication, partitioning, and sharding.
- [ ] Select relational, document, key–value, wide-column, graph, or search storage.

## Topic map

| Area | Topics | Focus |
|---|---|---|
| Relational | SQL, transactions, isolation levels, locking, deadlocks | Invariants and concurrency anomalies |
| Access paths | Indexes, composite/clustered/covering indexes, query optimization | Selectivity, ordering, write cost, query plans |
| Scale | Replication, partitioning, sharding | Read scaling, ownership, routing, rebalancing |
| NoSQL families | Document, key–value, wide-column, graph | Query model and consistency trade-offs |
| Products as examples | MongoDB, Cassandra, DynamoDB, Couchbase, Redis, Neo4j, Elasticsearch | Learn capabilities without making brand-first choices |

## বাংলা deep-learning prompt

একবারে একটি topic বসিয়ে prompt-টি কপি করুন: `SQL`, `Indexes`, `Composite Index`, `Clustered Index`, `Covering Index`, `Query Optimization`, `Transactions`, `Isolation Levels`, `Locking`, `Deadlock`, `Replication`, `Partitioning`, `Sharding`, `NoSQL`, `MongoDB`, `Cassandra`, `DynamoDB`, `Couchbase`, `Redis`, `Neo4j`, `Elasticsearch`, `Document DB`, `Key–Value DB`, `Wide-Column DB`, `Graph DB`।

```text
আমি Senior Backend Engineer/System Design interview-এর জন্য [TOPIC] শিখছি। Basic থেকে advanced পর্যন্ত পরিষ্কার বাংলায় গভীরভাবে শেখান; technical term-এর English নাম পাশে রাখবেন।

উত্তরটি এই structure-এ দিন:
1. সহজ সংজ্ঞা, mental model ও internal architecture
2. কোন data model/access pattern-এ এটি প্রয়োজন
3. read/write path step-by-step
4. একটি ছোট schema/query example এবং একটি production/real-life example
5. indexing, transaction, consistency, concurrency ও scaling details
6. relevant alternatives-এর comparison table এবং selection criteria
7. trade-offs, limitations, bottlenecks, hot spots ও failure modes
8. replication, partitioning, backup, security ও observability impact
9. 10×/100× scale-এ design কীভাবে বদলাবে
10. Senior/Staff interview explanation, common mistakes ও follow-up questions
11. 5টি basic, 5টি intermediate ও 5টি advanced practice question copyable code block-এ দিন
12. concise answer key শেষে আলাদা section-এ দিন

Topic: [TOPIC]
Context: Databases and Data Modeling
```

## Study path

1. Model users, orders, inventory, and payments with explicit invariants.
2. Create indexes for concrete queries; test column order and covering behavior conceptually or locally.
3. Map dirty reads, non-repeatable reads, phantoms, lost updates, and write skew to isolation choices.
4. Compare leader/follower replication with partitioning; include lag and failover.
5. Build a selection table for all database families using access pattern, scale, and consistency.

## Exercise

Design the data layer for limited-stock checkout. Prevent overselling, support order history, scale product reads, and explain how payments and inventory recover after partial failure.

## After-study checkpoint

1. Why can an index improve reads while hurting writes and storage?
2. How does composite-index column order affect usable queries?
3. Which anomalies can still occur under snapshot isolation?
4. What breaks when a sharding key is low-cardinality or monotonically increasing?
5. When is a document database a better fit than a relational database?

**Complete when:** your checkout model names its invariants, access paths, transaction boundaries, and scale strategy.

[← Networking](02-networking.md) · [Roadmap](../ROADMAP.md) · [Caching →](04-caching.md)
