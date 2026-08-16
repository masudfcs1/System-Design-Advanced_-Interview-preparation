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

## বাংলা deep-learning prompt

একবারে একটি topic বসিয়ে prompt-টি কপি করুন: `Repository`, `Factory`, `Strategy`, `Observer`, `Singleton`, `Builder`, `Decorator`, `Adapter`, `Dependency Injection`, `Hexagonal Architecture`, `Clean Architecture`, `Domain-Driven Design (DDD)`।

```text
আমি Senior Software Engineer/interview preparation-এর জন্য [TOPIC] শিখছি। Topic-টি basic থেকে advanced পর্যন্ত পরিষ্কার বাংলায় গভীরভাবে শেখান; English technical term পাশে রাখবেন।

উত্তরটি এই structure-এ দিন:
1. সহজ সংজ্ঞা, intent ও যে design force/problem solve করে
2. structure, participants এবং dependency direction
3. code/request/domain flow step-by-step
4. একটি ছোট code example এবং একটি production/real-life example
5. implementation variants ও testing considerations
6. related/competing patterns-এর comparison table
7. trade-offs, overengineering, coupling ও misuse cases
8. scalability, maintainability, team ownership ও evolution impact
9. কখন pattern-টি ব্যবহার না করাই ভালো
10. Senior/Staff interview explanation, common mistakes ও follow-up questions
11. 5টি basic, 5টি intermediate ও 5টি advanced practice question copyable code block-এ দিন
12. concise answer key শেষে আলাদা section-এ দিন

Topic: [TOPIC]
Context: Design Patterns and Software Architecture
```

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
