# System Design Worksheet

## 1. Problem and scope

- Users and use cases:
- In scope:
- Out of scope:
- Functional requirements:
- Non-functional requirements:

## 2. Scale estimates

| Signal | Assumption | Estimate |
|---|---|---:|
| Daily/monthly active users | | |
| Peak reads per second | | |
| Peak writes per second | | |
| Data added per day | | |
| Retention | | |
| Bandwidth | | |

Show simple arithmetic. Estimates guide decisions; they do not need false precision.

## 3. API and data model

### API surface

| Operation | Interface | Notes |
|---|---|---|
| | | |

### Core entities

| Entity | Key fields | Access patterns |
|---|---|---|
| | | |

## 4. High-level design

Draw clients, entry points, stateless services, stateful stores, asynchronous paths, and external dependencies. Label trust boundaries and data ownership.

## 5. Deep dives

- Critical read path:
- Critical write path:
- Partitioning and replication:
- Consistency and concurrency:
- Caching and invalidation:
- Async processing and delivery semantics:

## 6. Reliability and operations

- Failure modes and mitigations:
- Timeouts, retries, backpressure, and idempotency:
- Recovery point objective (RPO) / recovery time objective (RTO):
- Metrics, logs, traces, service-level indicators (SLIs), and alerts:
- Deployment, migration, and rollback:

## 7. Security and privacy

- Authentication and authorization:
- Encryption and secrets:
- Abuse prevention:
- Data classification, retention, and deletion:

## 8. Trade-offs and evolution

| Decision | Why now | Cost or risk | Trigger to revisit |
|---|---|---|---|
| | | | |

## Final review

- [ ] Requirements and scale drive the design.
- [ ] Every stateful component has ownership and a failure plan.
- [ ] Hot keys, bottlenecks, and cascading failures are addressed.
- [ ] At least two important alternatives are compared.
- [ ] The design can evolve without a high-risk rewrite.

