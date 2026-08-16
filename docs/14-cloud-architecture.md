# Module 14 · Cloud Architecture

**Goal:** Translate architecture needs into cloud capabilities without becoming vendor-dependent.  
**Suggested time:** 8–10 hours

## Learning outcomes

- [ ] Map compute, storage, database, network, identity, and edge needs to managed services.
- [ ] Explain virtual-network boundaries and shared responsibility.
- [ ] Compare virtual machines, containers, Kubernetes, and serverless compute.

## Topic map

| Capability | AWS examples | General decision |
|---|---|---|
| Compute | EC2, ECS, EKS, Lambda | Control, portability, scale pattern, operational burden |
| Data | S3, RDS, DynamoDB | Access pattern, consistency, capacity, administration |
| Edge/API | CloudFront, API Gateway | Caching, routing, authentication, quotas |
| Network/identity | VPC, IAM | Isolation, connectivity, least privilege |
| Alternatives | Azure and Google Cloud equivalents | Compare capabilities and guarantees, not names |

## বাংলা deep-learning prompt

একবারে একটি topic বসিয়ে prompt-টি কপি করুন: `AWS`, `Azure`, `GCP`, `EC2`, `ECS`, `EKS`, `Lambda`, `S3`, `RDS`, `DynamoDB`, `CloudFront`, `API Gateway`, `VPC`, `IAM`।

```text
আমি Senior/Staff Engineer/Cloud System Design interview-এর জন্য [TOPIC] শিখছি। Topic-টি basic থেকে advanced পর্যন্ত পরিষ্কার বাংলায় গভীরভাবে শেখান; English technical term পাশে রাখবেন।

উত্তরটি এই structure-এ দিন:
1. সহজ সংজ্ঞা, service model ও core use cases
2. shared-responsibility boundary এবং managed components
3. request/control/data flow step-by-step
4. একটি ছোট architecture example এবং একটি production/real-life example
5. networking, identity, availability, scaling ও pricing details
6. self-managed/managed এবং cloud alternatives-এর comparison table
7. limits, lock-in, quotas, hidden cost ও failure modes
8. multi-AZ/region recovery, security, observability ও governance
9. capacity ও cost optimization এবং migration considerations
10. Senior/Staff interview explanation, common mistakes ও follow-up questions
11. 5টি basic, 5টি intermediate ও 5টি advanced practice question copyable code block-এ দিন
12. concise answer key শেষে আলাদা section-এ দিন

Topic: [TOPIC]
Context: Cloud Architecture
```

## Study path

1. Draw public/private subnets, routes, gateways, load balancers, and data services.
2. Define workload and human identities using least privilege.
3. Compare managed and self-managed versions of one database and compute platform.
4. Design multi-zone availability and a regional recovery path.
5. Add budgets, tagging, quotas, audit logs, and infrastructure-as-code boundaries.

## Exercise

Place the document-sharing service from Module 12 in a cloud architecture. Show network zones, identities, compute, storage, database, edge, observability, and recovery.

## After-study checkpoint

1. Which responsibilities remain yours when using a managed service?
2. Why should a database usually not have a public endpoint?
3. When is serverless a poor fit?
4. How do identity policies and network controls complement each other?
5. What creates hidden cross-zone or cross-region cost?

**Complete when:** the architecture has explicit network, identity, availability, cost, and ownership boundaries.

[← Observability](13-observability.md) · [Roadmap](../ROADMAP.md) · [Kubernetes →](15-kubernetes.md)
