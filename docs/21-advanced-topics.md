# Module 21 · Staff and Principal Topics

**Goal:** Reason about organization-wide architecture, global operation, migration, and cost.  
**Suggested time:** 12+ hours, then ongoing practice

## Learning outcomes

- [ ] Design multi-tenant and multi-region systems with explicit isolation and consistency.
- [ ] Plan safe deployment, migration, workflow, and data-evolution strategies.
- [ ] Evaluate emerging architectures and operational cost at organizational scale.

## Topic map

| Theme | Topics | Core questions |
|---|---|---|
| Global architecture | Active-active, active-passive, multi-leader/cross-region replication, edge computing, global cache invalidation | Where does a write belong, and how are conflicts resolved? |
| Tenancy | Multi-tenant architecture | How are data, compute, quotas, keys, and noisy neighbors isolated? |
| Delivery | Zero-downtime, blue-green, canary, feature flags | How is risk limited, measured, and reversed? |
| Platform | Service mesh (Istio/Linkerd), serverless, distributed scheduling, distributed rate limiting | What capability is centralized and what is the operational cost? |
| Workflows/data | Temporal/Cadence, MVCC, CRDTs, CDC, vector databases, data mesh, streaming at scale | What state model and consistency boundary apply? |
| Economics | FinOps / cloud cost optimization | What unit cost, ownership, and guardrails influence design? |

## বাংলা deep-learning prompt

একবারে একটি topic বসিয়ে prompt-টি কপি করুন: `Multi-Tenant Architecture`, `Multi-Region Active-Active`, `Active-Passive Failover`, `Zero-Downtime Deployment`, `Blue-Green Deployment`, `Canary Deployment`, `Feature Flags`, `Service Mesh`, `Distributed Scheduling`, `Workflow Engines`, `MVCC`, `CRDTs`, `Vector Databases`, `Change Data Capture`, `Data Mesh`, `Event Streaming at Scale`, `Distributed Rate Limiting`, `Global Cache Invalidation`, `Multi-Leader Replication`, `Cross-Region Replication`, `Edge Computing`, `Serverless Architecture`, `FinOps`।

```text
আমি Staff/Principal Engineer এবং advanced System Design interview-এর জন্য [TOPIC] শিখছি। Topic-টি basic foundation থেকে expert depth পর্যন্ত পরিষ্কার বাংলায় শেখান; English technical term পাশে রাখবেন।

উত্তরটি এই structure-এ দিন:
1. সহজ সংজ্ঞা, mental model ও organization-scale problem
2. prerequisites, invariants, assumptions এবং guarantees
3. control/data/workflow architecture step-by-step
4. একটি ছোট example এবং একটি global production/real-life example
5. algorithms, protocols, migration ও operational details
6. competing architectures-এর comparison/decision table
7. consistency, availability, isolation, cost, limitations ও failure modes
8. security, compliance, observability, recovery ও team ownership
9. phased adoption/migration plan, compatibility ও rollback
10. business impact, unit economics এবং decision revisit triggers
11. Staff/Principal interview explanation, common mistakes ও adversarial follow-ups
12. 5টি basic, 5টি intermediate ও 5টি expert practice question copyable code block-এ দিন
13. concise answer key শেষে আলাদা section-এ দিন

Topic: [TOPIC]
Context: Staff and Principal-Level Architecture
```

## Study path

1. Design tenant isolation tiers: shared, partitioned, and dedicated.
2. Compare active-passive and active-active for a system with strict writes.
3. Create a zero-downtime schema and application migration using expand/contract.
4. Model a long-running workflow with durable execution and idempotent activities.
5. Compare MVCC, CRDT, multi-leader, and application conflict resolution.
6. Define unit economics and capacity/cost guardrails for the design.

## Capstone exercise

Design a globally distributed, multi-tenant collaboration platform. Include data residency, offline edits, conflict handling, tenant isolation, regional failure, safe deployments, feature flags, workflows, observability, and cost controls. Provide a phased migration from a single-region system.

## Staff-level review

1. Which decision is hardest to reverse, and how can you delay or de-risk it?
2. What happens organizationally when ownership crosses team boundaries?
3. Which consistency guarantee is visible to users?
4. How will the platform migrate without a synchronized cutover?
5. What is the cost per tenant or user, and what drives it?
6. Which risks require a prototype, load test, or operational game day?

## Completion criteria

- [ ] Produce the capstone architecture and a written decision log.
- [ ] Include migration phases, compatibility, rollback, and success metrics.
- [ ] Review it with at least one engineer acting as a skeptical stakeholder.
- [ ] Revise the design from feedback and record rejected alternatives.

**Complete when:** you can connect technical choices to user experience, team ownership, migration risk, and business cost.

[← Interview problems](20-interview-problems.md) · [Roadmap](../ROADMAP.md) · [Repository home](../README.md)
