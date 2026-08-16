# Module 15 · Kubernetes

**Goal:** Understand Kubernetes workload, networking, configuration, storage, and scaling primitives.  
**Suggested time:** 8–10 hours

## Learning outcomes

- [ ] Choose Deployment, StatefulSet, DaemonSet, and Job-style workloads.
- [ ] Explain Services, Ingress, configuration, secrets, storage, and namespaces.
- [ ] Design health checks, rollout, autoscaling, and basic service-mesh usage.

## Topic map

| Area | Topics | Focus |
|---|---|---|
| Workloads | Pod, ReplicaSet, Deployment, StatefulSet, DaemonSet | Reconciliation, identity, placement, rollout |
| Networking | Service, Ingress | Discovery, stable endpoints, north–south traffic |
| Configuration | ConfigMap, Secret, Namespace | Separation, access, rollout behavior |
| Capacity/storage | HPA, PersistentVolume | Metrics, requests/limits, state lifecycle |
| Ecosystem | Helm, service mesh | Packaging, traffic policy, identity, telemetry |

## বাংলা deep-learning prompt

একবারে একটি topic বসিয়ে prompt-টি কপি করুন: `Pod`, `Deployment`, `ReplicaSet`, `StatefulSet`, `DaemonSet`, `Service`, `Ingress`, `ConfigMap`, `Secret`, `HPA`, `PersistentVolume`, `Namespace`, `Service Mesh`, `Helm`।

```text
আমি Senior Backend/Platform Engineer এবং System Design interview-এর জন্য [TOPIC] শিখছি। Topic-টি basic থেকে advanced পর্যন্ত পরিষ্কার বাংলায় গভীরভাবে শেখান; English technical term পাশে রাখবেন।

উত্তরটি এই structure-এ দিন:
1. সহজ সংজ্ঞা, mental model ও Kubernetes-এ এর responsibility
2. control plane, controller ও resource relationships
3. create/reconcile/traffic/lifecycle flow step-by-step
4. একটি minimal YAML example এবং একটি production/real-life example
5. scheduling, probes, rollout, scaling, networking বা storage details
6. relevant Kubernetes resources/alternatives-এর comparison table
7. limits, misconfiguration, cascading failure ও troubleshooting scenarios
8. reliability, security, observability, upgrades ও operational safeguards
9. কখন resource/pattern-টি ব্যবহার করা উচিত নয়
10. Senior/Staff interview explanation, common mistakes ও follow-up questions
11. 5টি basic, 5টি intermediate ও 5টি advanced practice question copyable code block-এ দিন
12. concise answer key শেষে আলাদা section-এ দিন

Topic: [TOPIC]
Context: Kubernetes
```

## Study path

1. Trace desired state through controller reconciliation to running Pods.
2. Compare readiness, liveness, and startup probes and their failure impact.
3. Follow traffic from Ingress to Service to Pod.
4. Design requests, limits, disruption budgets, rollout, rollback, and autoscaling.
5. Add persistent storage only after defining data ownership and recovery.

## Exercise

Deploy or diagram a web API, worker, and stateful dependency. Include configuration, secrets, probes, autoscaling, disruption handling, and a safe release.

## After-study checkpoint

1. Why should liveness probes be conservative?
2. When is StatefulSet identity required?
3. What happens to in-flight work when a Pod terminates?
4. Why can CPU-based HPA be misleading?
5. Which problems does a service mesh add as well as solve?

**Complete when:** you can explain scheduling, traffic, rollout, failure, and state behavior end to end.

[← Cloud architecture](14-cloud-architecture.md) · [Roadmap](../ROADMAP.md) · [Microservices →](16-microservices.md)
