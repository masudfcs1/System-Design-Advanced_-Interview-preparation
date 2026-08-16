# Module 07 · Event-Driven Architecture

**Goal:** Design evolvable workflows around durable facts and clear ownership.  
**Suggested time:** 6–8 hours

## Learning outcomes

- [ ] Distinguish domain events, commands, notifications, and integration events.
- [ ] Explain event sourcing, CQRS, and saga trade-offs.
- [ ] Choose choreography or orchestration for a multi-step workflow.

## Topic map

| Area | Topics | Focus |
|---|---|---|
| Building blocks | Event bus, domain events | Facts, contracts, ownership, schemas |
| State model | Event sourcing, Command Query Responsibility Segregation (CQRS) | History, projections, rebuilds, complexity |
| Workflows | Saga, choreography, orchestration | Compensation, visibility, coupling |

## বাংলা deep-learning prompt

একবারে একটি topic বসিয়ে prompt-টি কপি করুন: `Event Bus`, `Domain Events`, `Event Sourcing`, `CQRS`, `Saga Pattern`, `Choreography`, `Orchestration`।

```text
আমি Senior/Staff Engineer/System Design interview-এর জন্য [TOPIC] শিখছি। Topic-টি basic থেকে advanced পর্যন্ত পরিষ্কার বাংলায় গভীরভাবে শেখান; English technical term পাশে রাখবেন।

উত্তরটি এই structure-এ দিন:
1. সহজ সংজ্ঞা, mental model ও business problem
2. event, command, state এবং ownership boundaries
3. end-to-end workflow step-by-step
4. একটি সহজ example এবং একটি production/real-life example
5. event contract, versioning, correlation, idempotency ও replay details
6. relevant patterns/alternatives-এর comparison table
7. trade-offs, coupling, compensation, consistency ও failure modes
8. recovery, observability, security ও operational complexity
9. কখন ব্যবহার করা উচিত নয়
10. Senior/Staff interview explanation, common mistakes ও follow-up questions
11. 5টি basic, 5টি intermediate ও 5টি advanced practice question copyable code block-এ দিন
12. concise answer key শেষে আলাদা section-এ দিন

Topic: [TOPIC]
Context: Event-Driven Architecture
```

## Study path

1. Rewrite ambiguous messages as past-tense facts or explicit commands.
2. Define event ownership, versioning, compatibility, metadata, and retention.
3. Model an order saga with both choreography and orchestration.
4. Separate business compensation from technical retry.
5. Evaluate when event sourcing/CQRS adds more operational cost than value.

## Exercise

Design a travel booking saga across flight, hotel, payment, and notification services. Handle a hotel failure after payment succeeds.

## After-study checkpoint

1. What makes a domain event different from a command?
2. How do you evolve an event without breaking old consumers?
3. When does choreography become hard to understand?
4. Why can compensation be impossible or only approximate?
5. When should CQRS not be used?

**Complete when:** your workflow has explicit ownership, correlation, compensation, observability, and replay behavior.

[← Messaging](06-messaging-systems.md) · [Roadmap](../ROADMAP.md) · [Storage →](08-storage-systems.md)
