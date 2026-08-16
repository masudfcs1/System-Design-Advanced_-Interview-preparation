# Module 01 · System Design Fundamentals

**Goal:** Build the vocabulary and decision framework used throughout the roadmap.  
**Suggested time:** 6–8 hours

## Learning outcomes

- [ ] Translate product requirements into measurable system qualities.
- [ ] Explain CAP, ACID/BASE, consistency, availability, latency, and throughput.
- [ ] Choose scaling and service-boundary approaches for a stated workload.
- [ ] Apply resilience patterns without causing retry storms or duplicate work.

## Topic map

| Area | Topics | Focus |
|---|---|---|
| Distributed trade-offs | CAP, consistency models, availability, partition tolerance | State what happens during a partition and what the user observes |
| Data semantics | ACID vs BASE, idempotency | Define invariants and safe repetition |
| Capacity | Latency vs throughput, horizontal vs vertical scaling | Identify the limiting resource and scaling unit |
| Service shape | Stateful vs stateless, monolith vs microservices, client–server | Prefer boundaries based on ownership and change patterns |
| Traffic management | API gateway, reverse proxy, L4/L7 load balancing, health checks | Know where routing, policy, and health decisions belong |
| Resilience | Rate limiting, circuit breaker, bulkhead, retry, timeout, backpressure | Bound work and prevent cascading failure |

---

# 📚 System Design Fundamentals: Basic to Advanced Masterclass (বাংলায়)

এই মডিউলে System Design-এর ২০টি ফাউন্ডেশনাল টপিককে বেসিক থেকে অ্যাডভান্সড (Senior/Staff Engineer ইন্টারভিউ লেভেল) পর্যন্ত বিস্তারিতভাবে সাজানো হয়েছে।

```
System Design Fundamentals
├── ১. Distributed Trade-offs & Theory (CAP, Availability, Partition Tolerance, Consistency Models)
├── ২. Data Semantics & Transaction Models (ACID vs BASE, Idempotency)
├── ৩. Capacity, Scaling & Performance (Latency vs Throughput, Horizontal vs Vertical Scaling)
├── ৪. Architecture & Service Paradigms (Client-Server, Stateless vs Stateful, Monolith vs Microservices)
├── ৫. Traffic Management & Routing (Reverse Proxy, Load Balancer L4/L7, API Gateway, Health Checks)
└── ৬. Resilience & Fault Tolerance (Rate Limiting, Circuit Breaker, Bulkhead, Retry & Timeout, Backpressure)
```

---

## পর্ব ১: Distributed Trade-offs & Theory (বিতরণকৃত সিস্টেমের মূলনীতি)

---

### ১. CAP Theorem

#### সহজ সংজ্ঞা ও Intuitive Mental Model
Eric Brewer-এর **CAP Theorem** বলে: একটি ডিস্ট্রিবিউটেড ডাটা স্টোরেজে নেটওয়ার্ক ফেইলিউর বা পার্টিশনের সময় আপনি একই সাথে **Consistency (C)** এবং **Availability (A)** দুটোই ১০০% গ্যারান্টি দিতে পারবেন না। যেকোনো একটি বেছে নিতে হবে।
- **Consistency (C):** প্রতিটি নোড থেকে রিড করলে সবসময় একদম লেটেস্ট রাইট করা ডাটা পাওয়া যাবে (Single source of truth)।
- **Availability (A):** ক্লাস্টার বা সার্ভারের যেকোনো নন-ফেইলিং নোড সবসময় সাকসেসফুল রেসপন্স রিটার্ন করবে (এমনকি ডাটা পুরোনো হলেও সে এরর দেবে না)।
- **Partition Tolerance (P):** নোডগুলোর মধ্যকার নেটওয়ার্কে প্যাকেট ড্রপ বা সংযোগ বিচ্ছিন্ন হলেও সিস্টেম ক্র্যাশ না করে চলবে।

> **Mental Model (বাস্তব অ্যানালজি):** ধরা যাক দুটি ব্যাংকের ব্রাঞ্চ (Node A ও Node B)। তাদের মধ্যকার ফোন লাইন কাটা পড়েছে (Network Partition)। এখন একজন গ্রাহক Node A-তে টাকা জমা করলেন। অন্য একজন গ্রাহক Node B থেকে ব্যালেন্স জানতে চাইলে Node B কী করবে?
> 1. কল ব্লক করে বলবে "ডাটা আপডেট হচ্ছে, অপেক্ষা করুন" (**CP System** - Consistency প্রাধান্য পেল)।
> 2. পুরোনো ব্যালেন্স রিটার্ন করবে (**AP System** - Availability প্রাধান্য পেল)।

#### কীভাবে কাজ করে ও প্রোডাকশন আর্কিটেকচার
- **CP Architecture (যেমন: Google Spanner, Apache HBase, ZooKeeper, Etcd):** কনসিস্টেন্সি রক্ষা করতে এরা কোরাম মেকানিজম বা ২-ফেজ কমিট (2PC) / Raft ব্যবহার করে। নেটওয়ার্ক পার্টিশন হলে পার্টিশনড মাইনরিটি নোডগুলোতে রাইট বা রিড বন্ধ হয়ে যায় (Unavailable)।
- **AP Architecture (যেমন: Apache Cassandra, Amazon DynamoDB, Couchbase):** নোডগুলো ডিসকানেক্টেড থাকলেও রিড/রাইট রিকোয়েস্ট এক্সেপ্ট করে এবং ব্যাকগ্রাউন্ডে **Gossip Protocol** বা **Hinted Handoff** দিয়ে পরে ডাটা সিঙ্ক (Eventual Consistency) করে।

#### Trade-offs & PACELC Theorem
- **বাস্তব সত্য:** ফিজিক্যাল নেটওয়ার্কে কেবল কাটা পড়বেই, তাই ডিস্ট্রিবিউটেড সিস্টেমে $P$ বাদ দেওয়ার কোনো সুযোগ নেই।
- **PACELC Theorem:** যদি পার্টিশন থাকে ($P$), তবে $A$ vs $C$; অন্যথায় (Else - স্বাভাবিক অবস্থায় $E$), ল্যাটেন্সি ($L$) vs কনসিস্টেন্সি ($C$)।

---

### ২. Availability (অ্যাভেইলেবিলিটি)

#### সহজ সংজ্ঞা ও মেজারমেন্ট
একটি সিস্টেমের সার্বক্ষণিক সচল থাকার এবং ক্লায়েন্টের রিকোয়েস্টে সফলভাবে রেসপন্স করার যোগ্যতাকে **Availability** বলে। এটি সাধারণত **"Nines"** দিয়ে পরিমাপ করা হয়:
- **99.9% (Three Nines):** বছরে ডাউনটাইম সর্বোচ্চ ৮.৭৬ ঘণ্টা।
- **99.99% (Four Nines):** বছরে ডাউনটাইম সর্বোচ্চ ৫২.৬ মিনিট।
- **99.999% (Five Nines - Telecom/Financial Standard):** বছরে ডাউনটাইম সর্বোচ্চ ৫.২৬ মিনিট।

$$\text{Availability} = \frac{\text{MTBF (Mean Time Between Failures)}}{\text{MTBF} + \text{MTTR (Mean Time To Repair)}} \times 100$$

#### প্রোডাকশন আর্কিটেকচার
- **Active-Passive Redundancy:** একটি প্রাইমারি ডাটাবেজ চলে, ফেইল করলে সেকেন্ডারি স্ট্যান্ডবাই টেকওভার করে (Heartbeat ও Failover স্ক্রিপ্ট দিয়ে)।
- **Active-Active Multi-Region (Netflix / Cloudflare):** একাধিক ডাটা সেন্টারে ট্রাফিক রাউট হয়। একটি পুরো ডাটা সেন্টার ডাউন হয়ে গেলেও অন্য রিজিয়ন ট্রাফিক হ্যান্ডেল করে।

---

### ৩. Partition Tolerance (পার্টিশন টলারেন্স)

#### সহজ সংজ্ঞা ও মেকানিজম
ডিস্ট্রিবিউটেড সিস্টেমের নোডগুলোর মাঝে নেটওয়ার্ক কমিউনিকেশন ধীর বা পুরোপুরি বিচ্ছিন্ন হয়ে গেলেও সিস্টেমটি ভেঙে না পড়ে ক্রিটিক্যাল অপারেশন চালিয়ে যাওয়ার ক্ষমতা।

#### Split-Brain প্রতিরোধ ও Quorum
যদি ১০টি নোডের ক্লাস্টার ৫টি-৫টি করে দুই ভাগে ভাগ হয়ে যায়, তবে দুটো অংশই নিজেকে লিডার দাবি করতে পারে (Split-Brain)। এটি রোধ করতে **Quorum Consensus** ($Q > N/2$) ব্যবহার করা হয়:
- রাইট বা লিডার নির্বাচনের জন্য অন্তত $\lfloor N/2 \rfloor + 1$ সংখ্যক নোডের সম্মতি লাগে। ফলে কেবল মেজরিটি অংশটি রাইট এক্সেপ্ট করে, মাইনরিটি অংশটি ইনঅ্যাক্টিভ থাকে।

---

### ৪. Consistency Models (কনসিস্টেন্সি মডেলসমূহ)

ডিস্ট্রিবিউটেড সিস্টেমে ডাটা রাইট করার পর অন্য নোড থেকে কখন এবং কীভাবে তা দেখা যাবে তার চুক্তি (Contract):

| মডেল | ব্যাখ্যা | বাস্তব উদাহরণ |
|---|---|---|
| **Strong Consistency (Linearizability)** | একটি নোডে রাইট সম্পন্ন হওয়ার সাথে সাথে অন্য যেকোনো নোড থেকে রিড করলে গ্যারান্টিসহ লেটেস্ট ভ্যালু পাওয়া যাবে। | ব্যাংক ব্যালেন্স, স্টক এক্সচেঞ্জ, Google Spanner |
| **Eventual Consistency** | নতুন কোনো আপডেট না আসলে নির্দিষ্ট সময় পর সব রেপ্লিকা একই মানে পৌঁছাবে। মাঝের সময়ে কিছু নোড পুরোনো ডাটা দেখাতে পারে। | DNS প্রপাগেশন, সোশ্যাল মিডিয়া লাইক কাউন্টার, YouTube View Count |
| **Read-Your-Writes Consistency** | যে ইউজার ডাটা আপডেট করেছে, সে নিজে সবসময় তার লেটেস্ট আপডেট দেখতে পাবে (অন্যরা হয়তো কয়েক সেকেন্ড পর দেখবে)। | Facebook কমেন্ট বা টুইট পোস্ট করার সাথে সাথে নিজের ফিডে দেখা |
| **Monotonic Reads** | একজন ইউজার একবার নতুন ডাটা দেখে ফেললে পরবর্তীতে পেজ রিফ্রেশ করলে কোনোভাবেই আগের পুরোনো ডাটা দেখতে পাবে না। | চ্যাট হিস্ট্রি বা মেসেজ টাইমলাইন স্ক্রলিং |

---

## পর্ব ২: Data Semantics & Transaction Models (ডাটাবেজ সেমান্টিকস)

---

### ৫. ACID vs BASE

```
[ ACID Model - Strong Integrity ]         [ BASE Model - High Scalability ]
  - Atomicity (All or Nothing)              - Basically Available (Never completely down)
  - Consistency (Rules & Constraints)       - Soft State (State changes over time)
  - Isolation (Independent Transactions)    - Eventual Consistency (Convergence guaranteed)
  (PostgreSQL, MySQL, CockroachDB)          (Cassandra, DynamoDB, MongoDB)
```

#### ACID (Relational Database-এর মূল ভিত্তি)
- **Atomicity:** ট্রানজ্যাকশনের সব অপারেশন সফল হবে, নয়তো একটিও সেভ হবে না (Rollback)।
- **Consistency:** ডাটাবেজ স্কিমা, ফরেন কি এবং ইউনিক কনস্ট্রেইন্ট সবসময় অক্ষুণ্ণ থাকবে।
- **Isolation:** সমান্তরাল ট্রানজ্যাকশনগুলো একে অপরকে প্রভাবিত করবে না (Isolation Levels: Read Uncommitted, Read Committed, Repeatable Read, Serializable)।
- **Durability:** একবার কমিট হলে সিস্টেম ক্র্যাশ বা পাওয়ার কাট হলেও ডাটা WAL (Write-Ahead Log)-এর মাধ্যমে পারসিস্টেন্ট থাকবে।

#### BASE (NoSQL ও ডিস্ট্রিবিউটেড সিস্টেমের দর্শন)
- **Basically Available:** আংশিক ফেইলিউর থাকলেও নোডগুলো রিড/রাইট রেসপন্স দেবে।
- **Soft State:** ব্যাকগ্রাউন্ড ডেপ্লিকেশনের কারণে কোনো এক্সটার্নাল রিকোয়েস্ট ছাড়াও ডাটার স্টেট পরিবর্তিত হতে পারে।
- **Eventual Consistency:** সময়ের ব্যবধানে সব নোড একই স্টেটে সিঙ্ক হবে।

---

### ৬. Idempotency (আইডেমপোটেন্সি)

#### সহজ সংজ্ঞা ও Mental Model
একই এপিআই বা অপারেশন একবার কল করলে যে ফলাফল হয়, নেটওয়ার্ক ফেইলিউরের কারণে ক্লায়েন্ট যদি একাধিকবার (ডুপ্লিকেট) কল করে, তবে সিস্টেমের স্টেট একই থাকবে এবং কোনো ক্ষতিকর পার্শ্বপ্রতিক্রিয়া (যেমন ডাবল পেমেন্ট) হবে না।

$$\text{API}(x) = \text{API}(\text{API}(x))$$

#### প্রোডাকশন আর্কিটেকচার (Stripe / bKash Payment Flow)
1. ক্লায়েন্ট পেমেন্ট রিকোয়েস্টের সাথে একটি ইউনিক `Idempotency-Key` (UUID v4) হেডার পাঠায়:
   ```http
   POST /api/v1/payments
   Idempotency-Key: 9b1deb4d-3b7d-4bad-9bdd-2b0d7b3dcb6d
   { "amount": 500, "currency": "BDT", "account": "017XXXXXXXX" }
   ```
2. **API Layer:** Redis-এ অ্যাটমিক `SET idempotency_key {status: "PROCESSING"} NX EX 120` দিয়ে লকিং করে।
3. যদি কি-টি আগে থেকেই থাকে:
   - স্ট্যাটাস `PROCESSING` হলে: `409 Conflict` বা অপেক্ষা করতে বলে।
   - স্ট্যাটাস `COMPLETED` হলে: ডাটাবেজ হিট না করে আগের সেভ করা রেসপন্স রিটার্ন করে।
4. পেমেন্ট সম্পন্ন হলে ডাটাবেজে রেকর্ড সেভ হয় এবং Redis-এ রেসপন্স বডি স্টোর করা হয়।

---

## পর্ব ৩: Capacity, Scaling & Performance (স্কেলিং ও পারফরম্যান্স)

---

### ৭. Latency vs Throughput

```
অ্যানালজি: মহাসড়ক (Highway System)
- Latency (ms): একটি গাড়ি কত দ্রুত গন্তব্যে পৌঁছাল।
- Throughput (RPS): প্রতি মিনিটে মহাসড়কের টোল প্লাজা দিয়ে মোট কতটি গাড়ি পার হতে পারল।
```

- **Latency:** একটি রিকোয়েস্ট শুরু হওয়া থেকে ক্লায়েন্টের কাছে রেসপন্স পৌঁছানো পর্যন্ত মোট সময় (পরিমাপ: Average, p50, p95, p99, p99.9)।
- **Throughput:** প্রতি সেকেন্ডে সিস্টেম মোট কতগুলো রিকোয়েস্ট সফলভাবে প্রসেস করতে পারে (পরিমাপ: Requests Per Second - RPS, Queries Per Second - QPS, Transactions Per Second - TPS)।

> **Senior Engineer Insight:** ইন্টারভিউতে কখনোই শুধু "Average Latency" বলবেন না; সবসময় **p99 / p99.9 (Tail Latency)** নিয়ে কথা বলুন, কারণ ১% স্লো রিকোয়েস্টই বড় সিস্টেমে ক্যাসকেডিং ফেইলিউরের প্রধান কারণ হয়।

---

### ৮. Horizontal vs Vertical Scaling

```
[ Vertical Scaling (Scale Up) ]            [ Horizontal Scaling (Scale Out) ]
      ┌──────────────┐                       ┌──────┐ ┌──────┐ ┌──────┐ ┌──────┐
      │  64 vCPU     │                       │ 2vCPU│ │ 2vCPU│ │ 2vCPU│ │ 2vCPU│
      │  256 GB RAM  │                       │ 8GB  │ │ 8GB  │ │ 8GB  │ │ 8GB  │
      └──────────────┘                       └──────┘ └──────┘ └──────┘ └──────┘
(একটি বড় শক্তিশালী সার্ভার)                    (একাধিক সাধারণ সার্ভারের ক্লাস্টার)
```

| প্যারামিটার | Vertical Scaling (Scale Up) | Horizontal Scaling (Scale Out) |
|---|---|---|
| **পদ্ধতি** | সিঙ্গেল মেশিনে CPU, RAM বা SSD বাড়ানো | লোড ব্যালেন্সারের পেছনে নতুন নোড যুক্ত করা |
| **হার্ডওয়্যার সীমা** | ফিজিক্যাল মেশিনের সর্বোচ্চ সীমা আছে | তাত্ত্বিকভাবে সীমাহীন স্কেলিং সম্ভব |
| **জটিলতা** | আর্কিটেকচারে কোনো পরিবর্তন লাগে না | ডিস্ট্রিবিউটেড স্টেট, নেটওয়ার্কিং ও ডাটাবেজ শার্ডিংয়ের জটিলতা তৈরি হয় |
| **খরচ** | হাই-এন্ড সুপার সার্ভারের দাম এক্সপোনেনশিয়াল হারে বাড়ে | কমদামী কমোডিটি সার্ভার ব্যবহারে খরচ অপ্টিমাইজড থাকে |

---

## পর্ব ৪: Architecture & Service Shape (সার্ভিস আর্কিটেকচার)

---

### ৯. Client-Server Architecture
ক্লায়েন্ট (ওয়েব, মোবাইল, IoT) রিকোয়েস্ট পাঠায় এবং সার্ভার সেন্ট্রালাইজড বিজনেস লজিক, সিকিউরিটি ও ডাটাবেজ এক্সেস নিয়ন্ত্রণ করে।
- **সুবিধা:** Separation of Concerns, প্ল্যাটফর্ম ইন্ডিপেন্ডেন্স এবং সেন্ট্রালাইজড ডাটা ম্যানেজমেন্ট।

---

### ১০. Stateless vs Stateful Services

```
Stateless Architecture:
[Client] ──> [Load Balancer] ──> [App Server 1] ──┐
                             ──> [App Server 2] ──┼──> [Shared Redis / DB] (State Store)
                             ──> [App Server 3] ──┘

Stateful Architecture:
[Client A] ──> [Server 1 (Session A in RAM)]
[Client B] ──> [Server 2 (Session B in RAM)]
```

- **Stateless Services:** সার্ভার কোনো লোকাল মেমোরি স্টেটের ওপর নির্ভর করে না। রিকোয়েস্টে প্রয়োজনীয় তথ্য (JWT বা Redis Session ID) থাকে। যেকোনো সার্ভার নোড ক্র্যাশ করলে অন্য সার্ভার তাৎক্ষণিক ট্রাফিক নিতে পারে।
- **Stateful Services:** সার্ভার লোকাল মেমোরিতে কানেকশন স্টেট বা ডাটা ধরে রাখে (যেমন: WebSocket চ্যাট সার্ভার, Multiplayer Game Session, Redis Node)। এতে Sticky Session বা Consistent Hashing প্রয়োজন হয়।

---

### ১১. Monolith vs Microservices

| ডাইমেনশন | Monolith Architecture | Microservices Architecture |
|---|---|---|
| **কোডবেস** | সিঙ্গেল কোডবেস, একটি ইউনিফাইড বাইনারি/আর্টিফ্যাক্ট | সার্ভিসভেদে আলাদা রিপোজিটরি ও স্বাধীন ডাটাবেজ |
| **ডিপ্লয়মেন্ট** | পুরো অ্যাপ্লিকেশন একসাথে ডিপ্লয় হয় | সার্ভিসভেদে স্বাধীনভাবে দিনে বহুবার ডিপ্লয় করা যায় |
| **ফেইলিউর বাউন্ডারি** | একটি মডিউলের মেমোরি লিক পুরো অ্যাপ ক্র্যাশ করায় | ফল্ট আইসোলেটেড থাকে (পেমেন্ট ডাউন হলেও ব্রাউজিং চলে) |
| **নেটওয়ার্ক ওভারহেড** | মেমোরিতে সরাসরি ফাংশন কল (Microseconds) | নেটওয়ার্কে gRPC / REST কল (Latency ও Network Failure রিস্ক) |
| **টিম অর্গানাইজেশন** | ছোট টিমের জন্য পারফেক্ট | একাধিক বড় টিম (Domain-Driven Design) স্বাধীনভাবে কাজ করতে পারে |

---

## পর্ব ৫: Traffic Management & Routing (ট্রাফিক ম্যানেজমেন্ট)

---

### ১২. Reverse Proxy vs Load Balancer vs API Gateway

```
Internet ──> [ L4 / L7 Load Balancer ] 
                   │
                   ▼
             [ API Gateway ] (Auth, Rate Limit, SSL Termination, Routing)
                   │
                   ▼
             [ Reverse Proxy (Nginx / Envoy) ] ──> [ Microservices Pods ]
```

1. **Reverse Proxy (Nginx, HAProxy, Envoy):** সার্ভারের সামনে বসে ক্লায়েন্টের রিকোয়েস্ট প্রসেস করে। কাজ: SSL Termination, Compression (Gzip/Brotli), Caching, Security Masking।
2. **Load Balancer:** মাল্টিপল সার্ভার নোডের মাঝে ট্রাফিক সুষমভাবে বণ্টন করে।
3. **API Gateway (Kong, Envoy, AWS API Gateway):** ক্লাউড নেটিভ মাইক্রোসার্ভিসের মূল এন্ট্রি পয়েন্ট। কাজ: Authentication/JWT Verification, Dynamic Rate Limiting, API Monetization/Billing, Request Transformation।

---

### ১৩. Load Balancer: Layer 4 (L4) vs Layer 7 (L7)

```
[Layer 4 (Transport Layer)]:
- রাউটিং ডিসিশন: IP Address + TCP/UDP Port।
- প্যাকেটের ভেতরের ডাটা বা HTTP হেডার খোলে না।
- অত্যন্ত দ্রুত, কম CPU ও মেমোরি ব্যবহার।

[Layer 7 (Application Layer)]:
- রাউটিং ডিসিশন: HTTP Path, Headers, Cookies, JSON Payload।
- উদাহরণ: /api/v1/orders -> Order Service, /images/* -> CDN/S3 Pods।
- SSL Decryption এবং ইন্টেলিজেন্ট কনটেন্ট-বেসড রাউটিং সাপোর্ট করে।
```

---

### ১৪. Health Checks (Liveness vs Readiness)

- **Liveness Probe:** "অ্যাপ্লিকেশনটি জীবিত আছে নাকি ডেডলক হয়ে আটকে আছে?"
  - ফেইল করলে: অর্কেস্ট্রেটর (Kubernetes) পডটি কিল করে রিস্টার্ট দেয়।
- **Readiness Probe:** "অ্যাপ্লিকেশনটি ডাটাবেজে কানেক্ট হয়ে নতুন ট্রাফিক হ্যান্ডেল করার জন্য প্রস্তুত কিনা?"
  - ফেইল করলে: সার্ভিস লোড ব্যালেন্সার টার্গেট গ্রুপ থেকে আইপি সাময়িকভাবে সরিয়ে দেয় (কোনো ট্রাফিক পাঠায় না), কিন্তু রিস্টার্ট করে না।

---

## পর্ব ৬: System Resilience & Fault Tolerance (সিস্টেম রেজিলিয়েন্স)

---

### ১৫. Rate Limiting

সিস্টেমকে অপব্যবহার (DDoS, Brute Force, Scraping) ও ট্রাফিক স্পাইক থেকে বাঁচাতে নির্দিষ্ট সময়ে রিকোয়েস্টের সংখ্যা সীমিত করা।

#### প্রধান অ্যালগরিদমসমূহ:
1. **Token Bucket:** একটি নির্দিষ্ট রেটে বাকেটে টোকেন রিফিল হয়। প্রতি রিকোয়েস্টে ১টি টোকেন কাটে। বাকেটে টোকেন থাকলে আকস্মিক ট্রাফিক স্পাইক (Burst) অ্যালাউ করে।
2. **Leaky Bucket:** একটি নির্দিষ্ট হারে ফিক্সড স্পিডে ড্রপ ড্রপ করে ট্রাফিক বের হয়। ট্রাফিককে একদম কনস্ট্যান্ট আউটপুটে পরিণত করে।
3. **Sliding Window Counter:** Redis-এ টাইমস্ট্যাম্প ও হ্যাশ ব্যবহার করে গত ৬০ সেকেন্ডের মুভিং উইন্ডোতে রিকোয়েস্ট লিমিট চেক করে (সবচেয়ে নিখুঁত)।

---

### ১৬. Circuit Breaker Pattern

ডাউন বা স্লো সার্ভিসে বারবার রিকোয়েস্ট পাঠিয়ে থ্রেড ব্লক হওয়া ও পুরো আর্কিটেকচার ধসে পড়া (Cascading Failure) রোধ করে।

```
        ┌──────────────┐
        │    CLOSED    │ ──(Failure Rate > Threshold)──> ┌────────────┐
        │ (Normal Ops) │                                 │    OPEN    │
        └──────────────┘ <──(Success Rate > Threshold)─┐ │(Fail-Fast) │
               ▲                                       │ └────────────┘
               │                                ┌────────────┐ │
               └─────────(Probe Success)────────│ HALF-OPEN  │<┘(Sleep Window Elapsed)
                                                └────────────┘
```

- **Closed:** সিস্টেম স্বাভাবিক, সব রিকোয়েস্ট ডাউনস্ট্রিমে যায়।
- **Open:** এরর রেট থ্রেশহোল্ড (যেমন: শেষ ২০ রিকোয়েস্টের ৫০% টাইমআউট) অতিক্রম করলে সার্কিট ওপেন হয়ে যায়। ডাউনস্ট্রিমে আর কল না করে তৎক্ষণাৎ ক্যাশড বা ফলব্যাক রেসপন্স দেয়।
- **Half-Open:** নির্দিষ্ট সময় পর পরীক্ষামূলকভাবে সীমিত কিছু রিকোয়েস্ট পাঠিয়ে দেখে ডাউনস্ট্রিম রিকভার করেছে কিনা।

---

### ১৭. Bulkhead Pattern

জাহাজের ভেতরের ওয়াটারটাইট কম্পার্টমেন্টের মতো সিস্টেমের রিসোর্সগুলোকে আলাদা পুলে ভাগ করা, যাতে একটি অংশের ব্যর্থতা পুরো সিস্টেমকে ডুবিয়ে না দেয়।
- **বাস্তব প্রয়োগ:** পেমেন্ট এপিআই-এর জন্য ডেডিকেটেড থ্রেড পুল (২০টি থ্রেড) এবং ইমেল নোটিফিকেশনের জন্য আলাদা থ্রেড পুল (১০টি থ্রেড)। ইমেল সার্ভার ক্র্যাশ করে তার থ্রেড ব্লক হলেও পেমেন্ট সিস্টেমের থ্রেড সম্পূর্ণ সচল থাকে।

---

### ১৮. Retry, Timeout, Exponential Backoff & Jitter

```
Interval = Base_Delay * (2 ^ attempt) + Random_Jitter
```

```
Without Jitter (Thundering Herd / Retry Storm):
Server Recovery ──> 50,000 Clients retry at EXACTLY the same 2.0s mark ──> Server Crashes Again!

With Full Jitter:
Server Recovery ──> Clients retry distributed randomly over [0, 2.0s] ──> Server Recovers Smoothly!
```

> **গোল্ডেন রুল:** নন-আইডেমপোটেন্ট (Non-idempotent) রিকোয়েস্টে রিট্রাই করবেন না। প্রতিটি আউটগোয়িং নেটওয়ার্ক কলেই সুনির্দিষ্ট **Timeout** ও **Deadlines (Context Propagation)** থাকতে হবে।

---

### ১৯. Backpressure (ব্যাকপ্রেশার)

যখন কোনো ডাউনস্ট্রিম কনজিউমার প্রডিউসারের পাঠানো স্পিডে ডাটা প্রসেস করতে পারে না, তখন কনজিউমার প্রডিউসারকে সিগন্যাল পাঠিয়ে ডাটা ট্রান্সমিশনের গতি কমাতে বাধ্য করে।
- **প্রয়োগ:** TCP Window Size Flow Control, Reactive Streams (Project Reactor / RxJava), Apache Kafka-তে Consumer Pull-based আর্কিটেকচার।

---

## 🎯 Top Practice Questions (Senior/Staff Level Interview Prep)

```markdown
### Basic Practice Questions (Tier 1)
1. Why does a network partition make 100% Availability and 100% Consistency simultaneously impossible in CAP theorem?
2. What is the fundamental operational difference between L4 and L7 Load Balancing?
3. Why is Stateless architecture strictly preferred over Stateful architecture for horizontal auto-scaling?
4. What HTTP status code and response headers should be returned when a Rate Limiter throttles a request?
5. How does a Kubernetes Readiness Probe differ from a Liveness Probe during deployment rollouts?

### Intermediate Practice Questions (Tier 2)
6. How do you implement a distributed Idempotency mechanism for a Payment API using Redis and relational DB transactions?
7. Explain the "Thundering Herd" problem during cache/service recovery, and how Exponential Backoff with Jitter prevents it.
8. How does the Token Bucket algorithm handle burst traffic differently compared to the Leaky Bucket algorithm?
9. Compare Eventual Consistency vs Read-Your-Writes consistency. How can you implement Read-Your-Writes on an Eventual Consistent master-replica database setup?
10. What are the 3 states of a Circuit Breaker? How do you configure error thresholds in high-throughput systems?

### Advanced / Staff-Level Practice Questions (Tier 3)
11. In an Active-Active Multi-Region distributed database, how does PACELC theorem influence the latency vs consistency trade-off when there is NO network partition?
12. Design a Bulkhead pattern for a microservice calling 5 downstream APIs where one API has a p99 latency of 10 seconds. How do you isolate resources using thread pools vs semaphores?
13. How does Backpressure propagate across an end-to-end distributed stream processing pipeline (e.g., IoT Device -> API Gateway -> Kafka -> Consumer Flink -> Elasticsearch)?
14. Under what exact conditions can an un-bounded retry loop cause a cascading catastrophic outage (Retry Storm)? How do you mathematically model and mitigate this?
15. If a database is ACID compliant on a single node, what additional distributed consensus algorithms (e.g., Raft, Paxos, 2PC) are required to maintain ACID across 5 geographically distributed shards?
```

---

## 🔑 Concise Answer Key / Self-Test Evaluation

<details>
<summary>👉 <b>Answer Key ও সমাধান দেখতে এখানে ক্লিক করুন</b></summary>

1. **CAP Trade-off:** নেটওয়ার্ক পার্টিশনে নোডগুলোর মধ্যে মেসেজ পাসিং বন্ধ হয়ে যায়। ক্লায়েন্ট ডাটা রাইট করতে চাইলে নোডটিকে হয় এরর দিয়ে অপারেশন রিজেক্ট করতে হয় ($A$ নষ্ট), নয়তো ডাটা এক্সেপ্ট করে অন্য নোডের সাথে অমিল তৈরি করতে হয় ($C$ নষ্ট)।
2. **L4 vs L7:** L4 আইপি ও টিসিপি/ইউডিপি পোর্ট দেখে প্যাকেট ফরওয়ার্ড করে (দ্রুত, লো সিপিইউ)। L7 পুরো এইচটিটিপি/এইচটিটিপিএস পে-লোড, পাথ ও কুকি ডিক্রিপ্ট করে কনটেন্ট-অ্যাওয়ার রাউটিং করে।
3. **Stateless Scaling:** সার্ভারে কোনো লোকাল স্টেট না থাকায় যেকোনো নতুন সার্ভার ক্লাস্টারে যুক্ত করে সাথে সাথে ট্রাফিক দেওয়া যায় এবং যেকোনো সার্ভার নোড ক্র্যাশ করলে ইউজার সেশন নষ্ট হয় না।
4. **Rate Limit Response:** `HTTP 429 Too Many Requests` সাথে `Retry-After: <seconds>`, `X-RateLimit-Limit`, `X-RateLimit-Remaining` হেডার।
5. **Liveness vs Readiness:** Liveness ফেইল করলে কন্টেইনার সাথে সাথে কিল হয়ে রিস্টার্ট নেয়। Readiness ফেইল করলে কন্টেইনার রিস্টার্ট হয় না, শুধুমাত্র সার্ভিস লোড ব্যালেন্সার থেকে ট্রাফিক সাময়িকভাবে বিচ্ছিন্ন করা হয়।
6. **Distributed Idempotency:** ক্লায়েন্ট `Idempotency-Key` পাঠায়। Redis-এ `SET key "PROCESSING" NX EX 120` দিয়ে লকিং হয়। ডাটাবেজে ট্রানজ্যাকশন শেষে ফলাফল সেভ হয় এবং Redis-এ রেসপন্স ক্যাশ হয়। পরবর্তীতে একই কি আসলে ক্যাশড রেসপন্স রিটার্ন করে।
7. **Backoff + Jitter:** ফেইল হওয়া হাজার হাজার ক্লায়েন্ট একই নির্দিষ্ট সেকেন্ডে রিট্রাই না করে র্যান্ডম টাইম গ্যাপিংয়ে রিকোয়েস্ট পাঠায়, ফলে রিকভার হতে থাকা সার্ভার আবার ক্র্যাশ করে না।
8. **Token vs Leaky Bucket:** Token Bucket হঠাৎ আসা ট্রাফিক বা স্পাইক (Burst) এক্সেপ্ট করতে পারে যদি বাকেটে টোকেন জমা থাকে। Leaky Bucket সবসময় কনস্ট্যান্ট স্মুথ রেটে ট্রাফিক রিলিজ করে।
9. **Read-Your-Writes on Replica:** ইউজার যখনই কোনো ডাটা আপডেট করে, পরবর্তী নির্দিষ্ট সময় (যেমন ৫ সেকেন্ড) তার রিড রিকোয়েস্টগুলো রিড-রেপ্লিকায় না পাঠিয়ে সরাসরি প্রাইমারি/মাস্টার ডিবিতে রাউট করা হয়।
10. **Circuit Breaker States:** `Closed` (স্বাভাবিক ট্রাফিক), `Open` (ফেইল-ফাস্ট, ডাউনস্ট্রিমে কল বন্ধ), `Half-Open` (পরীক্ষামূলক রিকভারি ট্রাফিক)।
11. **PACELC in Normal State:** পার্টিশন না থাকলেও ($E$) সিস্টেমকে ল্যাটেন্সি ($L$) এবং কনসিস্টেন্সি ($C$)-এর মধ্যে ট্রেড-অফ করতে হয়। স্ট্রং কনসিস্টেন্সি চাইলে ক্রস-রিজিয়ন নেটওয়ার্ক আরটিটি (Round Trip Time)-এর কারণে রাইট ল্যাটেন্সি বেড়ে যায়।
12. **Thread Pool vs Semaphore Bulkhead:** আইসোলেশনের জন্য ডেডিকেটেড থ্রেডপুল (Netflix Hystrix স্টাইল) বেশি নিরাপদ কারণ এটি নির্দিষ্ট ডাউনস্ট্রিমের জন্য থ্রেড বাউন্ড করে রাখে এবং হার্ড টাইমআউট দিয়ে আনরেস্পনসিভ থ্রেড রিলিজ করতে পারে।
13. **Distributed Backpressure:** কনজিউমার ডাটা প্রসেস করতে না পারলে কাফকা থেকে পোল করা বন্ধ রাখে। কাফকা ডিস্কে বাফার করে। গেটওয়েতে বাফার ফুল হলে ক্লায়েন্টকে `429` বা টিসিপি উইন্ডো সাইজ ০ পাঠিয়ে দেয়।
14. **Retry Storm Mitigation:** Exponential Backoff with Full Jitter, Circuit Breakers, Global Retry Budgets (সর্বোচ্চ ১০% ট্রাফিকের বেশি রিট্রাই এলাউ না করা), এবং Context Deadline/Cancellation propagation।
15. **Distributed ACID:** সিঙ্গেল নোডের ট্রানজ্যাকশন মাল্টি-নোডে এক্সটেন্ড করতে Two-Phase Commit (2PC) কোঅর্ডিনেশনের সাথে লিডার ইলেকশন ও কনকারেন্সি কন্ট্রোলের জন্য Paxos বা Raft কনসেনসাস অ্যালগরিদম এবং Spanner-এর মতো TrueTime/Hybrid Logical Clocks ব্যবহার করতে হয়।

</details>

---

## Study path

1. Write functional and non-functional requirements for a familiar product.
2. Compare strong, eventual, read-your-writes, and monotonic-read consistency.
3. Trace one request through DNS, gateway/proxy, load balancer, service, cache, and database.
4. Create a failure table for slow dependencies, overload, partial failure, and duplicate requests.
5. Explain why retries require deadlines, backoff, jitter, attempt limits, and idempotency.

## Exercise

Design the front door for a ticket-sale API that receives a 100× traffic spike. Include L4/L7 routing, health checks, per-user rate limits, timeouts, retry policy, circuit breaking, bulkheads, backpressure, and an idempotent purchase endpoint.

## After-study checkpoint

1. Why does CAP apply specifically when a network partition occurs?
2. When would vertical scaling be the best first move?
3. What is the difference between a reverse proxy, API gateway, and load balancer?
4. How can retries amplify an outage?
5. What state must an idempotency implementation persist, and for how long?

**Complete when:** you can defend the ticket API under overload and explain every resilience control’s boundary.

[← How to study](00-how-to-study.md) · [Roadmap](../ROADMAP.md) · [Networking →](02-networking.md)
