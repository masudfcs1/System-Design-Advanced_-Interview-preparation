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

## বাংলা deep-learning prompt

একবারে একটি topic বসিয়ে prompt-টি কপি করুন: `Service Discovery`, `API Gateway`, `Service Registry`, `Distributed Tracing`, `Inter-Service Communication`, `Database per Service`, `Shared Database`, `Saga`, `CQRS`, `Event-Driven Communication`।

```text
আমি Senior/Staff Engineer/System Design interview-এর জন্য [TOPIC] শিখছি। Topic-টি basic থেকে advanced পর্যন্ত পরিষ্কার বাংলায় গভীরভাবে শেখান; English technical term পাশে রাখবেন।

উত্তরটি এই structure-এ দিন:
1. সহজ সংজ্ঞা, mental model ও organizational/domain problem
2. service boundary, ownership এবং coupling-এর উপর impact
3. request/event/data flow step-by-step
4. একটি ছোট example এবং একটি production/real-life example
5. contracts, discovery, data ownership, migration ও versioning details
6. modular monolith/relevant alternatives-এর comparison table
7. latency, consistency, cascading failure ও operational complexity
8. resilience, security, observability, deployment ও team ownership
9. কখন microservice pattern-টি ব্যবহার করা উচিত নয়
10. Senior/Staff interview explanation, common mistakes ও follow-up questions
11. 5টি basic, 5টি intermediate ও 5টি advanced practice question copyable code block-এ দিন
12. concise answer key শেষে আলাদা section-এ দিন

Topic: [TOPIC]
Context: Microservices Architecture
```

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
