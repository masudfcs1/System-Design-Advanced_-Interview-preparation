# Module 12 · Security

**Goal:** Make security a set of explicit trust, identity, data, and abuse-control decisions.  
**Suggested time:** 7–9 hours

## Learning outcomes

- [ ] Separate authentication, authorization, delegation, and identity federation.
- [ ] Apply role-based and attribute-based access control.
- [ ] Protect data, secrets, APIs, and network edges using defense in depth.

## Topic map

| Area | Topics | Focus |
|---|---|---|
| Identity | Authentication, OAuth 2.0, OpenID Connect, JWT | Flows, token audience/scope, expiry, revocation |
| Access | Authorization, RBAC, ABAC | Least privilege and policy evaluation |
| Data | Encryption at rest/in transit, secrets management | Keys, rotation, envelope encryption, audit |
| Edge/API | API security, rate limiting, WAF, DDoS protection | Validation, quotas, filtering, absorption |

## বাংলা deep-learning prompt

একবারে একটি topic বসিয়ে prompt-টি কপি করুন: `Authentication`, `Authorization`, `RBAC`, `ABAC`, `OAuth 2.0`, `OpenID Connect`, `JWT`, `Encryption at Rest`, `Encryption in Transit`, `Secrets Management`, `API Security`, `Rate Limiting`, `WAF`, `DDoS Protection`।

```text
আমি Senior/Staff Engineer/System Design interview-এর জন্য [TOPIC] শিখছি। Topic-টি basic থেকে advanced পর্যন্ত পরিষ্কার বাংলায় গভীরভাবে শেখান; English technical term পাশে রাখবেন।

উত্তরটি এই structure-এ দিন:
1. সহজ সংজ্ঞা, threat model ও কোন risk এটি reduce করে
2. actors, assets, trust boundaries এবং assumptions
3. authentication/authorization/data flow step-by-step
4. একটি সহজ example এবং একটি production/real-life security example
5. keys/tokens/policies/rotation/audit-এর implementation details
6. relevant controls/alternatives-এর comparison table
7. attacks, bypasses, limitations, edge cases ও failure modes
8. least privilege, tenant isolation, privacy, monitoring ও incident response
9. secure design checklist এবং কখন control-টি যথেষ্ট নয়
10. Senior/Staff interview explanation, common mistakes ও follow-up questions
11. 5টি basic, 5টি intermediate ও 5টি advanced practice question copyable code block-এ দিন
12. concise answer key শেষে আলাদা section-এ দিন

Topic: [TOPIC]
Context: System and API Security
```

## Study path

1. Draw trust boundaries, identities, assets, and data flows for one system.
2. Model authentication and authorization as separate steps.
3. Compare session cookies and tokens; include theft and revocation.
4. Trace a secret from creation through distribution, rotation, and deletion.
5. Build layered controls for credential stuffing, injection, scraping, and volumetric attack.

## Exercise

Threat-model a multi-tenant document-sharing service. Include tenant isolation, link sharing, administrator roles, audit logs, encryption, secret rotation, and abuse controls.

## After-study checkpoint

1. Why is a valid JWT not enough to authorize a request?
2. When is ABAC worth its policy complexity?
3. Where are encryption keys stored and rotated?
4. How do WAF and rate limiting solve different problems?
5. What must every tenant-scoped query enforce?

**Complete when:** the design identifies assets, threats, controls, residual risk, and incident evidence.

[← Reliability](11-reliability.md) · [Roadmap](../ROADMAP.md) · [Observability →](13-observability.md)
