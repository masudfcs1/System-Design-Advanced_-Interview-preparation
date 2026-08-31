# Design Live-Streaming Platform

[Back to interview design problems](README.md) | [Practice guide](../20-interview-problems.md)

## Requirements

### Functional

- [ ] Define the three most important user journeys.
- [ ] State what is out of scope.

### Non-functional

- [ ] Availability:
- [ ] Latency:
- [ ] Consistency:
- [ ] Durability:
- [ ] Security and privacy:

## Capacity estimates

| Input | Assumption |
|---|---:|
| Daily active users | |
| Peak requests per second | |
| Read/write ratio | |
| Data generated per day | |
| Retention | |

## API and data model

Document the core APIs, entities, indexes, ownership boundaries, and retention rules.

## High-level design

Describe the request path, asynchronous work, storage, caching, partitioning, and external dependencies.

## Reliability and operations

- Failure modes and degraded behavior:
- Retry and idempotency boundaries:
- Observability and SLOs:
- Disaster recovery:
- Security controls:

## Bottlenecks and evolution

1. Identify the first likely bottleneck.
2. Explain the design at 10x and 100x scale.
3. Record at least three rejected alternatives and why.
4. Review the result with the [system design template](../../templates/system-design-template.md).

