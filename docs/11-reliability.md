# Module 11 · Reliability

**Goal:** Define reliability targets and design systems that fail predictably and recover safely.  
**Suggested time:** 6–8 hours

## Learning outcomes

- [ ] Distinguish high availability, fault tolerance, and disaster recovery.
- [ ] Define failover, backup, restore, RPO, and RTO.
- [ ] Use failure-mode analysis and chaos experiments responsibly.

## Topic map

| Area | Topics | Focus |
|---|---|---|
| Continuity | High availability, fault tolerance, failover | Redundancy, detection, switchover, degraded modes |
| Recovery | Disaster recovery, backup, restore, replication | RPO/RTO, integrity, restore testing |
| Validation | Chaos engineering | Hypothesis, blast radius, abort conditions, learning |

## বাংলা deep-learning prompt

একবারে একটি topic বসিয়ে prompt-টি কপি করুন: `High Availability`, `Fault Tolerance`, `Disaster Recovery`, `Failover`, `Backup`, `Restore`, `Replication`, `Chaos Engineering`।

```text
আমি Senior/Staff Engineer/System Design interview-এর জন্য [TOPIC] শিখছি। Topic-টি basic থেকে advanced পর্যন্ত পরিষ্কার বাংলায় গভীরভাবে শেখান; English technical term পাশে রাখবেন।

উত্তরটি এই structure-এ দিন:
1. সহজ সংজ্ঞা, mental model এবং reliability goal
2. availability, durability, RPO ও RTO-এর সঙ্গে সম্পর্ক
3. detection, mitigation, failover/recovery flow step-by-step
4. একটি সহজ example এবং একটি production incident/real-life example
5. redundancy, backup integrity, degraded mode ও restore testing details
6. relevant reliability approaches-এর comparison table
7. trade-offs, correlated failure, false failover ও hidden failure modes
8. monitoring, alerting, runbook, security ও chaos experiment safeguards
9. cost বনাম reliability target কীভাবে balance করব
10. Senior/Staff interview explanation, common mistakes ও follow-up questions
11. 5টি basic, 5টি intermediate ও 5টি advanced practice question copyable code block-এ দিন
12. concise answer key শেষে আলাদা section-এ দিন

Topic: [TOPIC]
Context: Reliability Engineering
```

## Study path

1. Define availability and durability targets for critical user journeys.
2. Create a failure-mode and effects analysis for each dependency.
3. Design zone failure, region failure, data corruption, and credential-loss recovery.
4. Write a backup/restore test with measured recovery time.
5. Propose one bounded chaos experiment and its safety controls.

## Exercise

Write a reliability plan for a payment API. Cover dependency failure, duplicate requests, regional outage, corrupted data, recovery validation, and degraded service.

## After-study checkpoint

1. Why does replication not replace backup?
2. How do RPO and RTO change architecture and cost?
3. What can make automated failover worse than waiting?
4. What is a useful degraded mode for payments?
5. What must be true before running a chaos experiment in production?

**Complete when:** the plan links each failure to detection, mitigation, recovery, and validation.

[← Scaling](10-scaling.md) · [Roadmap](../ROADMAP.md) · [Security →](12-security.md)
