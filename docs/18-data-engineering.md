# Module 18 · Data Engineering Concepts

**Goal:** Design data movement and analytical systems separately from transactional workloads.  
**Suggested time:** 6–8 hours

## Learning outcomes

- [ ] Distinguish ETL/ELT, warehouse/lake, and OLTP/OLAP.
- [ ] Choose batch or stream processing using latency and correctness needs.
- [ ] Design lineage, quality, schema evolution, and backfills.

## Topic map

| Area | Topics | Focus |
|---|---|---|
| Movement | ETL, ELT | Where transformation runs and how raw data is retained |
| Platforms | Data warehouse, data lake | Governance, schema, query performance, cost |
| Workloads | OLTP, OLAP | Transactions vs scans/aggregations |
| Processing | Batch, stream processing | Latency, windows, state, late data, replay |

## বাংলা deep-learning prompt

একবারে একটি topic বসিয়ে prompt-টি কপি করুন: `ETL`, `ELT`, `Data Warehouse`, `Data Lake`, `OLTP`, `OLAP`, `Stream Processing`, `Batch Processing`।

```text
আমি Senior Data/Backend Engineer এবং System Design interview-এর জন্য [TOPIC] শিখছি। Topic-টি basic থেকে advanced পর্যন্ত পরিষ্কার বাংলায় গভীরভাবে শেখান; English technical term পাশে রাখবেন।

উত্তরটি এই structure-এ দিন:
1. সহজ সংজ্ঞা, mental model ও business use case
2. sources, ingestion, processing, storage ও consumption architecture
3. end-to-end data flow step-by-step
4. একটি ছোট data example এবং একটি production/real-life example
5. schema, partitioning, windows, late data, lineage ও quality details
6. batch/stream, ETL/ELT বা relevant alternatives-এর comparison table
7. freshness, correctness, replay, cost, bottlenecks ও failure modes
8. backfill, privacy deletion, security, observability ও governance
9. scale এবং schema-evolution strategy
10. Senior/Staff interview explanation, common mistakes ও follow-up questions
11. 5টি basic, 5টি intermediate ও 5টি advanced practice question copyable code block-এ দিন
12. concise answer key শেষে আলাদা section-এ দিন

Topic: [TOPIC]
Context: Data Engineering
```

## Study path

1. Trace operational events into an analytical model.
2. Compare star-schema analytics with raw/curated lake zones.
3. Define event time, processing time, windows, watermarks, and late-data policy.
4. Add lineage, ownership, quality checks, and privacy deletion.
5. Design an idempotent backfill that can coexist with live ingestion.

## Exercise

Design analytics for a food-delivery marketplace: operational databases feed hourly finance reports and near-real-time delivery dashboards.

## After-study checkpoint

1. Why should analytical queries not run directly on the primary OLTP database?
2. When is ELT preferable to ETL?
3. How does late data change a windowed result?
4. What makes a pipeline replayable?
5. How can privacy deletion propagate through derived datasets?

**Complete when:** the design states freshness, correctness, lineage, replay, quality, and governance behavior.

[← Design patterns](17-design-patterns.md) · [Roadmap](../ROADMAP.md) · [Performance →](19-performance-optimization.md)
