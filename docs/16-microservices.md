# Module 16 · Microservices

**Goal:** Choose service boundaries and communication patterns based on organizational and domain needs.  
**Suggested time:** 7–9 hours

## Learning outcomes

- [ ] Decide when a modular monolith or microservices architecture is appropriate.
- [ ] Define service boundaries, data ownership, discovery, and contracts.
- [ ] Handle cross-service workflows, observability, and failure.

## Topic map

| Area | Topics | Focus |
|---|---|---|
| Edge/discovery | API gateway, service registry/discovery | Routing, policy, instance lifecycle |
| Communication | Synchronous and event-driven communication | Coupling, latency, availability, contracts |
| Data | Database per service, shared database | Ownership, joins, migrations, transactions |
| Workflows | Saga, CQRS | Distributed state and read models |
| Operations | Distributed tracing | Context, dependency graphs, ownership |

## Study path

1. Start with domain capabilities and ownership, not technical layers.
2. Compare a modular monolith with independently deployable services.
3. Design synchronous calls with deadlines and asynchronous flows with idempotency.
4. Plan contract and database migrations that support mixed versions.
5. Define ownership, on-call boundaries, and platform requirements.

## Exercise

Split—or deliberately keep together—an e-commerce system covering catalog, cart, checkout, inventory, payment, and shipping. Justify each boundary.

## After-study checkpoint

1. What organizational problem do microservices solve?
2. Why does a shared database weaken service autonomy?
3. How do long synchronous call chains affect availability?
4. What makes a service boundary stable?
5. When is a modular monolith the stronger design?

**Complete when:** every boundary has a business reason, owner, data model, contract, and failure strategy.

[← Kubernetes](15-kubernetes.md) · [Roadmap](../ROADMAP.md) · [Design patterns →](17-design-patterns.md)

