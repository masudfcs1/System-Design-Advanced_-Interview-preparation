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

