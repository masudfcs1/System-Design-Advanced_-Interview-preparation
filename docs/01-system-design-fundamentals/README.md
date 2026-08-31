# Module 01: System Design Fundamentals — Topic Guides

[← Back to Main Documentation Index](../README.md) · [Read the Complete Module Guide](../01-system-design-fundamentals.md)

Welcome to the topic-wise deep-dive study guide directory for **Module 01: System Design Fundamentals**. প্রতিটি ফাইলে বেসিক থেকে Senior/Staff Engineer লেভেল পর্যন্ত বিস্তারিত ধারণা, আর্কিটেকচার ডায়াগ্রাম, বাস্তব প্রোডাকশন উদাহরণ, ট্রেড-অফ টেবিল, ফেইলিউর মোড এবং ১৫টি করে ইন্টারভিউ প্রশ্ন ও সমাধান অন্তর্ভুক্ত রয়েছে।

---

## 📚 Categorized Topic Map

### ১. Distributed Trade-offs & Consistency (বিতরণকৃত সিস্টেমের মূলনীতি)
- 📌 [CAP Theorem](cap-theorem.md) — Brewer's theorem, PACELC, network partitions, and consistency vs availability trade-offs.
- 📌 [Consistency Models](consistency-models.md) — Linearizability, Sequential, Causal, Read-Your-Writes, Monotonic Reads, and Eventual Consistency.
- 📌 [Availability](availability.md) — The Math of "Nines", MTBF & MTTR, Active-Passive vs Active-Active redundancy, and Graceful Degradation.
- 📌 [Partition Tolerance](partition-tolerance.md) — Net-splits, Split-Brain prevention, Odd-numbered Quorums ($\lfloor N/2 \rfloor + 1$), and Raft/Paxos handling.

---

### ২. Data Semantics & Transactions (ডাটাবেজ সেমান্টিকস ও ট্রানজ্যাকশন)
- 📌 [ACID vs BASE](acid-vs-base.md) — Atomicity, Consistency, Isolation, Durability, ANSI SQL Isolation Levels, MVCC, and BASE scalability.
- 📌 [Idempotency](idempotency.md) — Safe retries, Idempotency Keys, Redis atomic locking (`SETNX`), and double-spend prevention.

---

### ৩. Capacity, Scaling & Performance (স্কেলিং ও পারফরম্যান্স)
- 📌 [Latency vs Throughput](latency-vs-throughput.md) — p50/p95/p99/p99.9 Tail Latency, Little's Law ($L = \lambda W$), Batching vs Streaming, and Saturation curves.
- 📌 [Horizontal vs Vertical Scaling](horizontal-vs-vertical-scaling.md) — Scale-Up vs Scale-Out, hardware ceilings, cost curves, and sharding trade-offs.

---

### ৪. Architecture & Service Shape (সার্ভিস আর্কিটেকচার)
- 📌 [Stateful vs Stateless Services](stateful-vs-stateless-services.md) — In-memory state, JWT vs Redis sessions, Sticky routing, WebSockets, and Connection Draining.
- 📌 [Monolith vs Microservices](monolith-vs-microservices.md) — Conway's Law, Domain-Driven Design (DDD), Modular Monoliths, and the Strangler Fig migration pattern.
- 📌 [Client-Server Architecture](client-server-architecture.md) — 2-Tier, 3-Tier, N-Tier, Thin vs Thick clients, "Never Trust the Client", and API versioning.

---

### ৫. Traffic Management & Edge Routing (ট্রাফিক ম্যানেজমেন্ট)
- 📌 [API Gateway](api-gateway.md) — Central entry point, Token verification, Rate limiting, SSL termination, and Backend-for-Frontend (BFF).
- 📌 [Reverse Proxy](reverse-proxy.md) — Forward vs Reverse proxy, Caching, Compression (Gzip/Brotli), Security masking, and Nginx/Envoy mechanics.
- 📌 [Load Balancer](load-balancer.md) — Round Robin, Least Connections, Power of Two Choices, Hardware vs Software, and Active-Passive VRRP.
- 📌 [Layer 4 vs Layer 7 Load Balancing](l4-vs-l7-load-balancing.md) — Transport (IP/Port) vs Application (HTTP/Path/Cookie) routing, AWS NLB vs ALB, and Two-Tier LB design.
- 📌 [Health Checks](health-checks.md) — Active vs Passive probing, Kubernetes Liveness vs Readiness vs Startup probes, Flapping mitigation, and Shallow vs Deep checks.

---

### ৬. System Resilience & Fault Tolerance (সিস্টেম রেজিলিয়েন্স)
- 📌 [Rate Limiting](rate-limiting.md) — Token Bucket, Leaky Bucket, Sliding Window Counter, and Distributed Redis Lua scripts.
- 📌 [Circuit Breaker](circuit-breaker.md) — Closed, Open, Half-Open states, Thread-pool vs Semaphore isolation, and Fallback strategies.
- 📌 [Bulkhead Pattern](bulkhead-pattern.md) — Nautical isolation, Resource partitioning, Thread pool sizing with Little's Law, and Cell-based architecture.
- 📌 [Retry](retry.md) — Transient vs Permanent errors, Idempotency mandate, Retry Storms, and Retry Budgets.
- 📌 [Timeout](timeout.md) — Connection vs Socket vs Request timeouts, Distributed Deadlines, and gRPC Context Cancellation.
- 📌 [Exponential Backoff and Jitter](exponential-backoff-and-jitter.md) — Thundering Herd prevention, Full Jitter vs Equal Jitter vs Decorrelated Jitter algorithms.
- 📌 [Backpressure](backpressure.md) — Flow control, Unbounded buffer crashes, TCP Windowing, Reactive Streams, and Kafka Pull architecture.

---

## 💡 How to Use These Topic Guides
1. প্রতিটি টপিকের শুরুতে **Learning Checklist** রিভিউ করুন।
2. **Deep Dive Notes**-এ সহজ সংজ্ঞা, অ্যানালজি, আর্কিটেকচার ডায়াগ্রাম এবং প্রোডাকশন কেস স্টাডিগুলো পড়ুন।
3. **Senior/Staff Engineer Interview Defense** সেকশনে দেওয়া কৌশল ও ফলো-আপ প্রশ্নগুলো আত্মস্থ করুন।
4. **Practice Questions** সেকশনের ১৫টি প্রশ্নের উত্তর নিজে নিজে দেওয়ার চেষ্টা করুন এবং শেষে ড্রপডাউনে **Answer Key** মিলিয়ে নিজেকে যাচাই করুন।
