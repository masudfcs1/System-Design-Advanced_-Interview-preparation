# Module 17 · Design Patterns and Architecture

**Goal:** Use patterns as vocabulary for recurring forces, not as mandatory structure.  
**Suggested time:** 7–9 hours

## Learning outcomes

- [ ] Recognize common creation, behavior, and structural patterns.
- [ ] Explain dependency injection, clean, and hexagonal boundaries.
- [ ] Apply domain-driven design (DDD) strategically to complex domains.

## Topic map

| Area | Topics | Focus |
|---|---|---|
| Creation | Factory, Builder, Singleton | Construction and lifecycle; avoid hidden global state |
| Behavior | Strategy, Observer | Replaceable policy and event notification |
| Structure | Decorator, Adapter, Repository | Composition, integration, persistence abstraction |
| Dependencies | Dependency injection | Explicit relationships and testability |
| Architecture | Hexagonal, Clean Architecture | Dependency direction and boundary isolation |
| Domain | DDD | Bounded contexts, ubiquitous language, aggregates |

## Study path

1. Identify the force each pattern addresses and the complexity it adds.
2. Refactor a conditional policy into Strategy and an integration into Adapter.
3. Keep domain rules independent from transport and persistence details.
4. Model one aggregate boundary around a real business invariant.
5. Map bounded contexts and their translation relationships.

## Exercise

Design the core of a payment-routing service supporting multiple providers, routing rules, retries, and audit records. Use only patterns that solve a named problem.

## After-study checkpoint

1. When does Repository hide useful database capabilities?
2. Why is Singleton often a lifecycle smell?
3. What direction should dependencies point in hexagonal architecture?
4. What invariant belongs inside an aggregate boundary?
5. How can the same word mean different things across bounded contexts?

**Complete when:** you can remove any pattern that lacks a concrete benefit and defend those that remain.

[← Microservices](16-microservices.md) · [Roadmap](../ROADMAP.md) · [Data engineering →](18-data-engineering.md)

