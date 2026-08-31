# CAP Theorem

[Back to System Design Fundamentals topics](README.md) · [Module guide](../01-system-design-fundamentals.md)

## 📌 Learning Checklist
- [ ] Explain CAP Theorem in your own words with intuitive mental models.
- [ ] Understand why "Pick 2 of 3" is a flawed simplification and why network partitions cannot be avoided.
- [ ] Differentiate between CP and AP systems under network partitions.
- [ ] Understand the PACELC theorem and how it extends CAP for normal operating conditions.
- [ ] Evaluate real-world trade-offs in distributed datastores (Spanner, Cassandra, DynamoDB, CockroachDB).
- [ ] Defend architecture decisions under network partition scenarios in Senior/Staff-level interviews.

---

## 📖 Deep Dive Notes

### 1. সহজ সংজ্ঞা ও Intuitive Mental Model

১৯৯৮ সালে কম্পিউটার বিজ্ঞানী Eric Brewer **CAP Theorem** (বা Brewer's Theorem) প্রস্তাব করেন, যা ২০০২ সালে Seth Gilbert এবং Nancy Lynch গাণিতিকভাবে প্রমাণ করেন। 

একটি ডিস্ট্রিবিউটেড ডাটা স্টোরেজে (Distributed Data Store) একই সাথে নিচের তিনটি বৈশিষ্ট্য শতভাগ নিশ্চিত করা তাত্ত্বিকভাবে অসম্ভব:
1. **Consistency (Linearizability / Strong Consistency):** প্রতিটি রিড অপারেশনে ক্লায়েন্ট সবসময় সর্বশেষ রাইট করা ডাটা পাবে, অথবা এরর পাবে—কখনোই পুরোনো বা স্টেল (stale) ডাটা পাবে না।
2. **Availability:** ক্লাস্টারের যেকোনো নন-ফেইলিং (non-failing) নোডে রিকোয়েস্ট পৌঁছালে সে সবসময় একটি নন-এরর সাকসেসফুল রেসপন্স রিটার্ন করবে (এমনকি সেই ডাটা সর্বশেষ আপডেট না হলেও)।
3. **Partition Tolerance:** নোডগুলোর মধ্যকার নেটওয়ার্ক কানেকশন ধীর, প্যাকেট ড্রপ বা সম্পূর্ণরূপে বিচ্ছিন্ন (Network Partition) হলেও পুরো সিস্টেম ক্র্যাশ না করে অপারেশন চালিয়ে যাবে।

> 🧠 **Intuitive Mental Model (বাস্তব জীবনের অ্যানালজি):**
> ধরা যাক দুটি ব্যাংকিং ব্রাঞ্চ: **ব্রাঞ্চ A** (ঢাকা) এবং **ব্রাঞ্চ B** (চট্টগ্রাম)। তারা একটি প্রাইভেট ফাইবার অপটিক নেটওয়ার্কে যুক্ত। 
> হঠাৎ রাস্তায় খোঁড়াখুঁড়ির কারণে ফাইবার অপটিক ক্যাবল কাটা পড়ল (**Network Partition**)।
> ঠিক এই মুহূর্তে একজন গ্রাহক ঢাকা ব্রাঞ্চে ১০,০০০ টাকা জমা করলেন। কয়েক সেকেন্ড পর তার নমিনি চট্টগ্রাম ব্রাঞ্চে গিয়ে একাউন্ট ব্যালেন্স চেক করতে চাইলেন।
> এখন চট্টগ্রাম ব্রাঞ্চের সামনে দুটি বিকল্প পথ খোলা আছে:
> 1. **বিকল্প ১ (Consistency বেছে নেওয়া - CP System):** চট্টগ্রাম ব্রাঞ্চ বলবে: *"আমাদের ঢাকার সাথে সংযোগ বিচ্ছিন্ন, তাই টাকা উত্তোলনের ব্যালেন্স কনফার্ম করতে পারছি না। অনুগ্রহ করে অপেক্ষা করুন।"* — অর্থাৎ সিস্টেম সেবা দিতে অস্বীকৃতি জানাল (**Availability বিসর্জন দিল**)।
> 2. **বিকল্প ২ (Availability বেছে নেওয়া - AP System):** চট্টগ্রাম ব্রাঞ্চ গ্রাহককে তার পুরোনো ব্যালেন্স দেখিয়ে দিল এবং টাকা তুলতে দিল। কিন্তু বাস্তবে একাউন্টে আরও ১০,০০০ টাকা জমা হয়েছিল যা চট্টগ্রাম এখনো জানে না (**Consistency বিসর্জন দিল**)।

---

### 2. কেন "Pick 2 out of 3" একটি মারাত্মক ভুল ধারণা?

সফটওয়্যার ইন্ডাস্ট্রিতে বহুল প্রচলিত একটি ভুল স্লোগান হলো: *"CAP থেকে যেকোনো ২টি বেছে নাও (CA, CP, বা AP)"*। 
বাস্তব প্রোডাকশনে **CA (Consistency + Availability without Partition Tolerance) বলে কোনো ডিস্ট্রিবিউটেড সিস্টেম বাস্তবে সম্ভব নয়!**

- ফিজিক্যাল নেটওয়ার্ক কখনো ১০০% নির্ভরযোগ্য হতে পারে না। রাউটার ফেইল করবে, ক্যাবল কাটা পড়বে, ক্লাউড প্রোভাইডারের সুইচ রিস্টার্ট নেবে এবং GC Pause-এর কারণে নোড সাময়িক বিচ্ছিন্ন হবে।
- সুতরাং, ডিস্ট্রিবিউটেড সিস্টেমে **Partition Tolerance (P) ঐচ্ছিক নয়, বাধ্যতামূলক**।
- আসল ট্রেড-অফ হলো: **"যখনই নেটওয়ার্ক পার্টিশন ঘটবে ($P$), আপনি কি Consistency ($C$) প্রাধান্য দিয়ে অপারেশন বন্ধ রাখবেন (CP), নাকি Availability ($A$) প্রাধান্য দিয়ে সাময়িক অসঙ্গতি মেনে নেবেন (AP)?"**

```
                  ┌───────────────────────┐
                  │   Network Partition   │
                  │       Occurred?       │
                  └───────────┬───────────┘
                              │
               ┌──────────────┴──────────────┐
               ▼                             ▼
            [ YES ]                       [ NO ]
    ┌──────────────────────┐      ┌──────────────────────┐
    │ Choose between:      │      │ PACELC Trade-off:    │
    │  - Consistency (CP)  │      │ Choose between:      │
    │  - Availability (AP) │      │  - Latency (L)       │
    └──────────────────────┘      │  - Consistency (C)   │
                                  └──────────────────────┘
```

---

### 3. Architecture & Step-by-Step Flow

ধরা যাক একটি ৩-নোডের ক্লাস্টার ($N_1, N_2, N_3$):

```
       [Client 1] (Write: x = 5)             [Client 2] (Read: x = ?)
           │                                      │
           ▼                                      ▼
      ┌─────────┐      Network Partition     ┌─────────┐
      │  Node 1 │ < - - - - - X - - - - - - >│  Node 2 │
      │ (x = 5) │                            │ (x = 0) │
      └─────────┘                            └─────────┘
           │                                      │
           │                                      │
           └ - - - - - - - - - - - - - - - - - - ─┘
                           Node 3 (x = 0)
```

#### CP System Execution Flow (যেমন: Google Spanner, Apache HBase, ZooKeeper, Etcd):
1. **Client 1** $N_1$-এ `x = 5` রাইট রিকোয়েস্ট পাঠায়।
2. $N_1$ কোরাম কনসেনসাস ($Q = \lfloor N/2 \rfloor + 1 = 2$) অর্জনের জন্য $N_2$ ও $N_3$-এ রেপ্লিকেশন রিকোয়েস্ট পাঠায়।
3. নেটওয়ার্ক পার্টিশনের কারণে মেসেজ পৌঁছায় না। $N_1$ প্রয়োজনীয় কোরাম পায় না।
4. **ফলাফল:** $N_1$ রিকোয়েস্ট ফেইল করায় এবং ক্লায়েন্টকে এরর/টাইমআউট রিটার্ন করে (`HTTP 500/503`)। সিস্টেম **Consistency** ধরে রাখতে নিজের সার্ভিস আনঅ্যাভেইলেবল করে দেয়।

#### AP System Execution Flow (যেমন: Apache Cassandra, Amazon DynamoDB, Couchbase):
1. **Client 1** $N_1$-এ `x = 5` রাইট রিকোয়েস্ট পাঠায়।
2. $N_1$ লোকাল স্টোরেজে `x = 5` সেভ করে ক্লায়েন্টকে তাৎক্ষণিক `HTTP 200 OK` রিটার্ন করে।
3. **Client 2** ঠিক একই মুহূর্তে পার্টিশনড $N_2$-এ রিড রিকোয়েস্ট পাঠায়।
4. $N_2$ ক্লায়েন্টকে পুরোনো মান `x = 0` রিটার্ন করে।
5. **ফলাফল:** উভয় ক্লায়েন্ট সফল রেসপন্স পেল (**Availability** নিশ্চিত), কিন্তু ক্লায়েন্টরা ভিন্ন ভিন্ন ডাটা দেখল (**Inconsistent**)। পার্টিশন ঠিক হলে ব্যাকগ্রাউন্ডে Gossip Protocol বা Read Repair দিয়ে ডাটা সিঙ্ক হয়।

---

### 4. PACELC Theorem: বাস্তব জীবনের পূর্ণাঙ্গ মডেল

Daniel Abadi ২০১১ সালে **PACELC Theorem** প্রণয়ন করেন, কারণ CAP Theorem কেবল নেটওয়ার্ক ফেইলিউরের মুহূর্তের কথা বলে—কিন্তু ৯৯.৯% সময় যখন নেটওয়ার্ক সম্পূর্ণ সুস্থ থাকে, তখন কী ঘটে?

$$\mathbf{If\ P\ (Partition) \rightarrow Choose\ A\ or\ C;\ Else\ (Normal) \rightarrow Choose\ L\ or\ C}$$

- **PC/EC (যেমন: Spanner, CockroachDB):** পার্টিশনের সময় Consistency (CP), স্বাভাবিক অবস্থায়ও ল্যাটেন্সি মেনে নিয়ে স্ট্রং Consistency (Synchronous replication across nodes)।
- **PA/EL (যেমন: Cassandra, DynamoDB):** পার্টিশনের সময় Availability (AP), স্বাভাবিক অবস্থায়ও কম ল্যাটেন্সির জন্য Asynchronous replication (Eventual consistency)।

---

### 5. Alternatives & Comparison Matrix

| বৈশিষ্ট্য / সিস্টেম | CP Datastore (Spanner, Etcd, CockroachDB) | AP Datastore (Cassandra, DynamoDB, Couchbase) | CA RDBMS (Single-node Postgres, MySQL) |
|---|---|---|---|
| **Consensus Algorithm** | Raft, Paxos, 2PC (Two-Phase Commit) | Gossip Protocol, Merkle Trees, Vector Clocks | Single-node ACID / Master-Worker Replica |
| **Partition Response** | Error / Timeout / Write rejection | Stale Read / Concurrent Write Conflict | Master ড্রপ করলে ড্রপআউট; ক্লাস্টারিং ছাড়া CA সম্ভব নয় |
| **Data Synchronization** | Synchronous Replication (Quorum based) | Asynchronous Replication (Tunable Quorum) | Replication Lag সহ Master-to-Replica sync |
| **Latency Profile** | তুলনামূলক বেশি (Consensus wait-time) | অত্যন্ত কম (Immediate local acknowledgement) | লোকাল সিঙ্গেল নোডে ন্যূনতম ল্যাটেন্সি |
| **আদর্শ ব্যবহারের ক্ষেত্র** | ব্যাংক ব্যালেন্স, ইনভেন্টরি স্টক, অর্ডার প্রসেসিং | চ্যাট হিস্ট্রি, IoT সেন্সর ডেটা, সোশ্যাল মিডিয়া ফিড | একক ডাটাবেজ নির্ভর ট্র্যাডিশনাল ওয়েব অ্যাপ্লিকেশন |

---

### 6. Trade-offs, Failure Modes & Mitigations

#### Failure Mode 1: Split-Brain Syndrome (দ্বিমুখী বিভাজন)
- **ঝুঁকি:** নেটওয়ার্ক পার্টিশনের কারণে ক্লাস্টার দুভাগ হয়ে উভয় অংশই নিজেদের "লিডার" বা অথরিটেটিভ মনে করে আলাদা আলাদা রাইট এক্সেপ্ট করে।
- **শনাক্তকরণ:** ডেটাবেজে আইডির সংঘর্ষ (Duplicate Keys) বা রিড কনফ্লিক্ট।
- **প্রতিরোধ (Mitigation):** **Odd-numbered Quorum ($2N+1$ nodes)** প্রয়োগ করা। যেকোনো রাইট বা ডিসিশনের জন্য $\lfloor N/2 \rfloor + 1$ মেজরিটি নোডের ভোট বাধ্যতামূলক করা। মাইনরিটি পার্টিশন স্বয়ংক্রিয়ভাবে রিড-অনলি মোডে চলে যায়।

#### Failure Mode 2: Cascading Latency during Network Degradation
- **ঝুঁকি:** নেটওয়ার্ক পুরোপুরি কাটা না পড়ে যদি প্যাকেট ড্রপ রেট ১০%-২০% বৃদ্ধি পায়, তবে CP সিস্টেমের নোডগুলো কনসেনসাস রিট্রাই করতে করতে কানেকশন পুল নিঃশেষ করে পুরো সিস্টেম ডাউন করতে পারে।
- **প্রতিরোধ:** এগ্রেসিভ টাইমআউট, সার্কিট ব্রেকার এবং লোকাল হার্টবিট সেন্সর।

---

### 7. Senior / Staff Engineer Interview Defense

> **ইন্টারভিউয়ারের ফাঁদ:** *"আমাদের সিস্টেমে ১০০% Consistency এবং ১০০% Availability দুটোই লাগবে। আপনি কোন ডেটাবেজ বেছে নেবেন?"*
>
> 💡 **Staff-Level উত্তর দেওয়ার কৌশল:**
> "তাত্ত্বিকভাবে এবং ফিজিক্যাল নেটওয়ার্কের বাস্তবতায় এটি একটি অসম্ভব দাবি (CAP Theorem ও FLP Impossibility Result)। নেটওয়ার্ক পার্টিশন একটি অনিবার্য ফিজিক্যাল সত্য। আমাদের আলোচনা করতে হবে **বিজনেসের রিকয়ারমেন্ট** নিয়ে:
> 1. যদি এটি **ফিনান্সিয়াল ট্রানজ্যাকশন বা টিকিটিং ইনভেন্টরি** হয়: আমরা **CP আর্কিটেকচার** বেছে নেব (যেমন Google Spanner বা CockroachDB)। নেটওয়ার্ক পার্টিশন ঘটলে আমরা সাময়িক ডাউনটাইম বা ফেইল-ফাস্ট এরর মেনে নেব, কিন্তু কখনোই ভুল ব্যালেন্স বা ডাবল-স্পেন্ডিং হতে দেব না।
> 2. যদি এটি **সোশ্যাল মিডিয়া লাইক, কমেন্ট বা ভিডিও স্ট্রিমিং মেটাডাটা** হয়: আমরা **AP আর্কিটেকচার** বেছে নেব (যেমন DynamoDB বা Cassandra)। সাময়িক স্টেল ডেটা গ্রহণযোগ্য, কিন্তু ব্যবহারকারীকে ফাঁকা স্ক্রিন দেখানো যাবে না।
> 3. সাধারণ অবস্থায় আমরা **PACELC ট্রেড-অফ** বিবেচনা করে Tunable Quorum ($R + W > N$) ব্যবহার করে ল্যাটেন্সি ও কনসিস্টেন্সির ফাইন-টিউনিং করব।"

---

## 📝 Practice Questions

```markdown
### Basic Practice Questions
1. CAP Theorem-এর তিনটি উপাদানের (C, A, P) সংজ্ঞা দিন।
2. "CA" আর্কিটেকচার কেন বাস্তব ডিস্ট্রিবিউটেড সিস্টেমে অসম্ভব?
3. CP সিস্টেমে নেটওয়ার্ক পার্টিশন ঘটলে ক্লায়েন্ট কী রেসপন্স পায়?
4. AP সিস্টেমের ডেটাবেজ কীভাবে পরবর্তীতে নোডগুলোর মধ্যে ডেটা সিঙ্ক করে?
5. PACELC থিওরেমের পূর্ণরূপ এবং উদ্দেশ্য কী?

### Intermediate Practice Questions
6. Cassandra-তে Tunable Consistency ($R + W > N$) ব্যবহার করে কীভাবে একটি AP সিস্টেমকে গাণিতিকভাবে Strong Consistency দেওয়া যায়?
7. Google Spanner কীভাবে TrueTime (GPS + Atomic Clocks) ব্যবহার করে বিশ্বজুড়ে CP সিস্টেম পরিচালনা করে?
8. Split-Brain প্রতিরোধে ৩-নোড ক্লাস্টারের চেয়ে ৪-নোড ক্লাস্টার কি বেশি রেজিলিয়েন্ট? কেন?
9. একটি পেমেন্ট গেটওয়েতে CP না AP—কোন ডিজাইন মডেল প্রযোজ্য এবং কেন?
10. নেটওয়ার্ক পার্টিশন শেষ হওয়ার পর কনকারেন্ট কনফ্লিক্ট কীভাবে সমাধান করা হয় (Last-Write-Wins বনাম Vector Clocks)?

### Advanced / Staff-Level Questions
11. Multi-Region Active-Active ডিপ্লয়মেন্টে PACELC থিওরেম অনুযায়ী স্বাভাবিক অবস্থায় (Normal condition) ল্যাটেন্সির ওপর কী প্রভাব পড়ে?
12. Raft বা Paxos কনসেনসাস অ্যালগরিদম কীভাবে নিশ্চিত করে যে একটি নেটওয়ার্ক পার্টিশনের মাইনরিটি অংশ কোনো রাইট কমিট করতে পারবে না?
13. CAP থিওরেমের গাণিতিক প্রমাণে (Gilbert & Lynch) "Linearizability" ধারণার গুরুত্ব কী?
14. মাইক্রোসার্ভিস আর্কিটেকচারে যখন সার্ভিস A (CP) সার্ভিস B (AP)-এর ওপর নির্ভর করে, তখন সামগ্রিক সিস্টেমের CAP প্রোফাইল কী দাঁড়ায়?
15. ব্যাংকিং সিস্টেমে যদি Core Ledger হয় CP, তবে ইউজার মোবাইল অ্যাপকে হাই-অ্যাভেইলেবল (AP-like experience) দেখাতে আপনি কী ধরণের আর্কিটেকচারাল প্যাটার্ন (Saga, Outbox, CQRS) প্রয়োগ করবেন?
```

---

## 🔑 Answer Key & Self-Test Evaluation

<details>
<summary>👉 <b>Answer Key ও মূল্যায়ন দেখতে এখানে ক্লিক করুন</b></summary>

1. **CAP সংজ্ঞা:** C = প্রতিটি নোডে লেটেস্ট ডাটা রিড (Linearizability); A = প্রতিটি নন-ফেইলিং নোড থেকে সাকসেসফুল রেসপন্স; P = নেটওয়ার্ক মেসেজ ড্রপ বা পার্টিশন হলেও সিস্টেম ফাংশনাল থাকা।
2. **CA অসম্ভব কেন:** ডিস্ট্রিবিউটেড সিস্টেম মানেই একাধিক ফিজিক্যাল মেশিন যা নেটওয়ার্ক দিয়ে যুক্ত। ফিজিক্যাল কেবল বা সুইচ কখনো ১০০% ফেইলিউর-মুক্ত হতে পারে না। তাই $P$ বাদ দেওয়ার কোনো বাস্তব সুযোগ নেই।
3. **CP রেসপন্স:** কোরাম না পেলে বা মাইনরিটি পার্টিশনে থাকলে সিস্টেম রিকোয়েস্ট রিজেক্ট করে `HTTP 503 Service Unavailable` বা টাইমআউট এরর দেয়।
4. **AP ডেটা সিঙ্ক:** Gossip Protocol, Read Repair (রিড করার সময় অসম ডেটা পেলে ব্যাকগ্রাউন্ডে আপডেট করা), Hinted Handoff এবং Anti-Entropy (Merkle Tree sync) মেকানিজম দিয়ে।
5. **PACELC:** If Partition ($P$) $\rightarrow$ choose Availability ($A$) or Consistency ($C$); Else ($E$) $\rightarrow$ choose Latency ($L$) or Consistency ($C$)। এটি স্বাভাবিক অবস্থায় ল্যাটেন্সি বনাম কনসিস্টেন্সির ট্রেড-অফ ব্যাখ্যা করে।
6. **Tunable Quorum:** রেপ্লিকেশন ফ্যাক্টর $N$, রিড কোরাম $R$, রাইট কোরাম $W$ হলে, যদি $R + W > N$ হয় (Pigeonhole Principle), তবে রিড কোরামের অন্তত একটি নোডে নিশ্চিতভাবে সর্বশেষ রাইট থাকবে।
7. **Google Spanner TrueTime:** জিপিএস রিসিভার এবং অ্যাটমিক ক্লক ব্যবহার করে সময় নির্ণয়ের ত্রুটি ($\epsilon \approx 1-7$ms) বাউন্ডেড রাখে এবং কমিট ওয়েট (Commit Wait) মেকানিজম দিয়ে গ্লোবালি লিনিয়ারাইজেবল ট্রানজ্যাকশন প্রদান করে।
8. **৩ বনাম ৪ নোড রেজিলিয়েন্স:** ৩ নোডের জন্য কোরাম $\lfloor 3/2 \rfloor + 1 = 2$ (১টি ফেইলিউর টলারেট করে)। ৪ নোডের জন্য কোরাম $\lfloor 4/2 \rfloor + 1 = 3$ (এখানেও মাত্র ১টি ফেইলিউর টলারেট করতে পারে)। ফলে ৪ নোডে অতিরিক্ত কোনো ফল্ট টলারেন্স বাড়ে না, উল্টো নেটওয়ার্ক ওভারহেড বাড়ে।
9. **পেমেন্ট গেটওয়েতে CP:** ডাবল স্পেন্ডিং এবং ব্যালেন্স নেগেটিভ হওয়া প্রতিরোধে ডেটা কনসিস্টেন্সি শতভাগ অক্ষুণ্ণ রাখা বাধ্যতামূলক।
10. **কনফ্লিক্ট রেজোলিউশন:** LWW (Last-Write-Wins) ঘড়ির ড্র্রিফট বা স্কিউয়ের কারণে ডাটা হারাতে পারে; Vector Clocks বা CRDTs (Conflict-free Replicated Data Types) কার্যকারণ সম্পর্ক (causality) ট্র্যাক করে কনফ্লিক্ট মুক্ত মার্জ নিশ্চিত করে।
11. **Multi-Region PACELC:** স্বাভাবিক অবস্থায়ও যদি স্ট্রং কনসিস্টেন্সি (PC/EC) চাওয়া হয়, তবে প্রতিটি রাইটে মহাদেশীয় দূরত্বের ফাইবার অপটিক্সের রাউন্ড-ট্রিপ টাইম (RTT: ~100-200ms) যুক্ত হবে, ফলে ল্যাটেন্সি অনেক বৃদ্ধি পাবে।
12. **Paxos/Raft Minority Defense:** লিডারকে যেকোনো লগ এন্ট্রি কমিট করার জন্য মেজরিটি নোডের অ্যাকনলেজমেন্ট পেতে হয়। মাইনরিটি পার্টিশনের নোড সংখ্যা $\le N/2$ হওয়ায় তারা কখনোই মেজরিটি ভোট সংগ্রহ করে লগ কমিট করতে পারে না।
13. **Linearizability in CAP:** গিলবার্ট ও লিঞ্চ প্রমাণ করেন যে CAP-এর $C$ মূলত "Linearizability"—অর্থাৎ বিশ্বজুড়ে একটি গ্লোবাল ক্লক বা টাইমলাইন থাকলে সমস্ত অপারেশন যেভাবে একের পর এক সম্পন্ন হতো, সিস্টেমকে ঠিক সেভাবে আচরণ করতে হবে।
14. **CP + AP কম্পোজিশন:** সামগ্রিক এন্ড-টু-এন্ড সিস্টেম তার সবচেয়ে দুর্বল লিঙ্কের মতো আচরণ করে। যদি একটি রিকোয়েস্ট সফল হতে CP সার্ভিসের ওপর সিঙ্ক্রোনাসলি নির্ভর করতে হয়, তবে পুরো রিকোয়েস্ট পাথটি CP-এর মতো ডাউনটাইমের শিকার হবে।
15. **CQRS & Saga Pattern:** রিড পাথে Eventual Consistency সমৃদ্ধ AP ভিউ (Elasticsearch/Redis Read Model) ইউজারকে ইনস্ট্যান্ট রেসপন্স দেয়; রাইট পাথে অ্যাসিঙ্ক্রোনাস মেসেজ কিউ (Kafka) এবং Saga Orchestration দিয়ে CP কোর লেজারে ট্রানজ্যাকশন নিরাপদে এক্সিকিউট করা হয়।

</details>
