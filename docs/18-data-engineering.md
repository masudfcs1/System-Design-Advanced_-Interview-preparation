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

