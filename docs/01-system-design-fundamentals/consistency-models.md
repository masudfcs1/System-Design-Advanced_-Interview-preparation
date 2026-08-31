# Consistency Models (কনসিস্টেন্সি মডেলসমূহ)

[Back to System Design Fundamentals topics](README.md) · [Module guide](../01-system-design-fundamentals.md)

## 📌 Learning Checklist
- [ ] Understand the spectrum of consistency from Strong (Linearizable) to Eventual.
- [ ] Clearly explain Linearizability vs Sequential Consistency vs Causal Consistency.
- [ ] Understand Client-centric consistency models: Read-Your-Writes, Monotonic Reads, Monotonic Writes, and Writes-Follow-Reads.
- [ ] Learn how Quorum intersection ($R + W > N$) guarantees strong consistency.
- [ ] Analyze real-world trade-offs between consistency, latency, and availability.
- [ ] Defend data consistency design choices in Staff-level interviews.

---

## 📖 Deep Dive Notes

### 1. সহজ সংজ্ঞা ও Intuitive Mental Model

ডিস্ট্রিবিউটেড সিস্টেমে যখন ডেটা একাধিক নোড বা রেপ্লিকায় (replicas) কপি থাকে, তখন একটি নোডে ডেটা রাইট বা আপডেট করার পর অন্য নোডগুলো থেকে রিড করলে ক্লায়েন্ট কখন, কোন ক্রমে এবং কী মানের ডেটা দেখতে পাবে—তার সুনির্দিষ্ট গ্যারান্টি বা চুক্তিকে (Contract) **Consistency Model** বলে।

> 🧠 **Intuitive Mental Model:**
> ধরা যাক একটি নোটিশ বোর্ড। 
> - **Strong Consistency (Linearizability):** প্রিন্সিপাল স্যার বোর্ডে নতুন ছুটির নোটিশ লাগানোর সাথে সাথে স্কুলের প্রতিটা শিক্ষার্থী (যে ক্লাসরুমেই থাকুক) তাৎক্ষণিকভাবে সেই নতুন নোটিশটি দেখতে পায়।
> - **Eventual Consistency:** প্রিন্সিপাল স্যার নোটিশ বোর্ডে লাগালেন, কিন্তু দূরবর্তী ক্লাসরুমগুলোতে নোটিশটি পিয়ন হেঁটে হেঁটে পৌঁছে দিচ্ছে। কিছু ক্লাসরুম ৫ মিনিট পর জানবে, কিছু ১০ মিনিট পর। কিন্তু দিনের শেষে সবাই ঠিকই নোটিশটি জেনে যাবে।
> - **Read-Your-Writes:** আপনি ফেসবুক বা টুইটারে একটি কমেন্ট পোস্ট করলেন। অন্য বন্ধুরা হয়তো ৫ সেকেন্ড পর দেখবে, কিন্তু আপনি নিজে পেজ রিফ্রেশ করলে সাথে সাথে নিজের কমেন্টটি দেখতে পাবেন।

---

### 2. The Consistency Spectrum (শক্তিশালী থেকে শিথিল)

ডিস্ট্রিবিউটেড কনসিস্টেন্সিকে দুটি প্রধান ভাগে ভাগ করা যায়:
1. **Data-Centric Models:** সমস্ত ক্লায়েন্টের দৃষ্টিকোণ থেকে সিস্টেমের বৈশ্বিক ডেটা অর্ডার কেমন দেখাবে।
2. **Client-Centric Models:** একজন নির্দিষ্ট ক্লায়েন্ট বা সেশনের দৃষ্টিকোণ থেকে ডেটার ধারাবাহিকতা কেমন থাকবে।

```
[ STRICTEST / SLOWEST ]
       │
       ▼  Linearizability (Strong Consistency / External Consistency)
       │  Sequential Consistency (Lamport's logical ordering)
       │  Causal Consistency (Happens-before causality preserved)
       │  Read-Your-Writes Consistency (Client sees their own updates)
       │  Monotonic Reads / Monotonic Writes (No time-travel backward)
       ▼  Eventual Consistency (Fastest, High Availability, Replicas converge eventually)
[ WEAKEST / FASTEST ]
```

---

### 3. প্রধান Data-Centric Consistency Models

```
Linearizability (Strong):
Client A:  |─── Write(x=5) ───|
Client B:                           |─── Read(x) -> 5 (Guaranteed) ───|

Eventual Consistency:
Client A:  |─── Write(x=5) ───|
Replica 1: [x=5]
Replica 2: [x=0] ──(Sync Lag)──> [x=5] (After delay)
Client B:                 |─── Read(x) -> 0 (Stale Data!) ───|
```

#### ১. Linearizability (Strong Consistency)
- **চুক্তি:** একবার একটি রাইট অপারেশন সফলভাবে সম্পন্ন (Commit) হলে, বিশ্বজুড়ে এর পরবর্তী যেকোনো রিড অপারেশন অবশ্যই সেই নতুন ভ্যালুটি পাবে।
- **বাস্তবায়ন:** Raft, Paxos, Multi-Paxos বা TrueTime-সহ 2PC।
- **বাস্তব উদাহরণ:** Google Cloud Spanner, Etcd, CockroachDB, ব্যাংক একাউন্ট ব্যালেন্স।

#### ২. Sequential Consistency (Leslie Lamport)
- **চুক্তি:** সমস্ত প্রসেসরের অপারেশনগুলো একটি নির্দিষ্ট সিকোয়েন্সে কার্যকর হবে এবং প্রতিটি প্রসেসের নিজস্ব অপারেশন তার প্রোগ্রামের ক্রম মেনে চলবে। তবে এটি বৈশ্বিক রিয়েল-টাইম ক্লকের ওপর নির্ভরশীল নয়।
- **বাস্তব উদাহরণ:** সোশ্যাল মিডিয়া কমেন্ট থ্রেডের ইন্টারনাল লজিক্যাল অর্ডার।

#### ৩. Causal Consistency
- **চুক্তি:** যে অপারেশনগুলোর মধ্যে কার্যকারণ সম্পর্ক (Causality / Cause-and-Effect) আছে, সেগুলোকে অবশ্যই সঠিক ক্রমে এক্সিকিউট করতে হবে। স্বাধীন (Concurrent) অপারেশনগুলো ভিন্ন ভিন্ন ক্রমে দেখতে সমস্যা নেই।
- **বাস্তব উদাহরণ:** মেসেজের রিপ্লাই। মূল মেসেজ আসার আগেই যেন রিপ্লাই না ভেসে ওঠে (Vector Clocks / Version Vectors দিয়ে ট্র্যাক করা হয়)।

#### ৪. Eventual Consistency
- **চুক্তি:** সিস্টেমে যদি নতুন কোনো রাইট না আসে, তবে সময়ের ব্যবধানে সমস্ত রেপ্লিকা একই মানে পৌঁছে যাবে (Convergence)। মাঝের সময়ে বিভিন্ন নোড ভিন্ন ভিন্ন মান দেখাতে পারে।
- **বাস্তব উদাহরণ:** DNS রেকর্ড আপডেট, YouTube ভিউ কাউন্টার, Amazon প্রোডাক্ট রিভিউ।

---

### 4. Client-Centric Consistency Models

| মডেল | ক্লায়েন্টের প্রতি গ্যারান্টি | ভঙ্গ হলে যে অস্বাভাবিক আচরণ (Anomaly) দেখা যায় |
|---|---|---|
| **Read-Your-Writes** | একজন ক্লায়েন্ট যে পরিবর্তন করেছে, সে পরবর্তীতে নিজে রিড করলে সবসময় সেই পরিবর্তন দেখতে পাবে। | ইউজার পোস্ট পাবলিশ করার পর পেজ রিফ্রেশ করে নিজের পোস্ট খুঁজে পায় না। |
| **Monotonic Reads** | একজন ইউজার একবার একটি নতুন মান দেখে ফেললে, পরবর্তীতে সে আর কখনো পেছনের পুরোনো মান দেখতে পাবে না। | রিফ্রেশ দেওয়ার পর লাইভ চ্যাটে নতুন মেসেজ উধাও হয়ে আবার পুরোনো মেসেজ ফিরে আসা (Time Travel)। |
| **Monotonic Writes** | একজন ইউজারের একাধিক রাইট অপারেশন সিস্টেমে ঠিক সেই ক্রমানুসারেই কার্যকর হবে যেভাবে সে পাঠিয়েছিল। | প্রোফাইল ছবি আপলোড করার পর ক্যাপশন এডিট করলে ক্যাপশন আগে আর ছবি পরে সেভ হওয়া। |
| **Writes-Follow-Reads** | একজন ইউজার ডেটা রিড করার পর যদি নতুন কোনো রাইট পাঠায়, তবে সেই রাইটটি সবসময় ওই রিড করা ভার্সনের ওপর নির্ভর করবে। | অন্যের কমেন্ট পড়ার পর করা রিপ্লাই মূল কমেন্টের আগে গিয়ে যুক্ত হওয়া। |

---

### 5. Tunable Consistency: কোরাম ইন্টারসেকশন ($R + W > N$)

Apache Cassandra ও Amazon DynamoDB-এর মতো সিস্টেমে ক্লায়েন্ট প্রতি কুয়েরিতে নিজের পছন্দমতো কনসিস্টেন্সি লেভেল টিউন করতে পারে:
- $N$ = Total Replicas (যেমন: ৩)
- $W$ = Write Quorum (কতটি নোড রাইট নিশ্চিত করবে)
- $R$ = Read Quorum (কতটি নোড থেকে রিড তুলনা করা হবে)

$$\mathbf{R + W > N \implies Strong\ Consistency\ (Overlapping\ Quorum)}$$

```
Replicas: [ Node 1 ]   [ Node 2 ]   [ Node 3 ]
Write(W=2):  [OK]          [OK]           --
Read(R=2):    --           [OK]          [OK]
                           ▲
             Overlapping Node (Guaranteed latest timestamp)
```
- যদি $W = 2$ এবং $R = 2$ হয় ($N = 3$), তবে $2 + 2 = 4 > 3$। অর্থাৎ রিড করা নোডগুলোর মাঝে অন্তত একটি নোড থাকবে যাতে সর্বশেষ রাইটটি বিদ্যমান ছিল!

---

### 6. Alternatives & Trade-offs Comparison Matrix

| কনসিস্টেন্সি মডেল | রিড/রাইট ল্যাটেন্সি | অ্যাভেইলেবিলিটি রেটিং | জটিলতা | ডাটাবেজ উদাহরণ |
|---|---|---|---|---|
| **Linearizability** | সর্বোচ্চ (Cross-node consensus) | কম (পার্টিশনে ব্লক) | খুব বেশি (Paxos/Raft) | Google Spanner, CockroachDB |
| **Causal** | মাঝারি (Metadata overhead) | বেশি | মাঝারি (Vector Clocks) | MongoDB (Causal Sessions), Riak |
| **Read-Your-Writes** | কম | বেশি | মাঝারি (Sticky routing/Cookies) | Master-Replica MySQL + Sticky Session |
| **Eventual** | সর্বনিম্ন (Local node write) | সর্বোচ্চ | কম (LWW / Merkle sync) | Cassandra, DynamoDB, Couchbase |

---

### 7. Failure Modes & Production Mitigations

#### Failure Mode 1: Master-Replica Replication Lag-এ Read-Your-Writes নষ্ট হওয়া
- **দৃশ্যপট:** ইউজার মাস্টার ডাটাবেজে প্রোফাইল পিকচার আপডেট করল। রিড ট্রাফিক কমানোর জন্য প্রোফাইল ভিউ রিড-রেপ্লিকাতে পাঠানো হয়। রেপ্লিকা ১০০ms পিছিয়ে থাকায় ইউজার তার পুরোনো ছবি দেখতে পায়।
- **প্রতিরোধ (Mitigation):**
  1. **User Pinning to Master:** রাইট করার পর পরবর্তী ১০-১৫ সেকেন্ডের সমস্ত রিড রিকোয়েস্ট সরাসরি মাস্টার ডিবিতে পাঠানো (Session Cookie দিয়ে ট্র্যাক করা)।
  2. **Replication GTID / LSN Tracking:** ক্লায়েন্ট রেসপন্সে সর্বশেষ ট্রানজ্যাকশন আইডি (Log Sequence Number) পাঠানো। রেপ্লিকা সেই LSN পর্যন্ত সিঙ্ক না হওয়া পর্যন্ত রিকোয়েস্ট ব্লক রাখবে অথবা মাস্টারে ফরোয়ার্ড করবে।

#### Failure Mode 2: Concurrent Writes & Clock Skew (Last-Write-Wins Data Loss)
- **ঝুঁকি:** Eventual Consistency সিস্টেমে নোডগুলোর সার্ভার ক্লকের সময়ের সামান্য পার্থক্যের (Clock Drift) কারণে পরের রাইট আগের বলে চিহ্নিত হয়ে মুছে যায় (Silent Data Loss)।
- **প্রতিরোধ:** NTP-নির্ভর Wall-clock টাইমস্ট্যাম্পের বদলে Logical Clocks (Lamport Clocks, Hybrid Logical Clocks বা CRDTs) ব্যবহার করা।

---

### 8. Senior / Staff Engineer Interview Defense

> **ইন্টারভিউয়ার:** *"আমাদের সিস্টেমে সোশ্যাল ফিডে ১ বিলিয়ন ইউজার আছে। আমরা কি Linearizability ব্যবহার করব?"*
>
> 💡 **Staff-Level উত্তরের কাঠামো:**
> "সোশ্যাল মিডিয়া ফিডের জন্য Linearizability ব্যবহার করা একটি বিশাল ওভারকিল এবং পারফরম্যান্স বিপর্যয় ডেকে আনবে।
> 1. **বিজনেস প্রয়োজনীয়তা বিশ্লেষণ:** সোশ্যাল ফিডে লাইক বা কমেন্ট ২ সেকেন্ড দেরিতে পৌঁছালে বিজনেসের কোনো আর্থিক ক্ষতি নেই। কিন্তু ল্যাটেন্সি ১০০ms বাড়লে ইউজার এঙ্গেজমেন্ট উল্লেখযোগ্য হারে কমে যায়।
> 2. **আমাদের পছন্দনীয় মডেল:** আমরা বেছে নেব **Eventual Consistency with Read-Your-Writes & Monotonic Reads**।
> 3. **আর্কিটেকচারাল বাস্তবায়ন:** সামগ্রিক ফিড AP স্টাইলে ক্যাশ ও রিড রেপ্লিকা থেকে সার্ভ হবে (Eventual Consistency)। তবে ব্যবহারকারীর নিজস্ব পোস্টের জন্য অপটিমিস্টিক ইউআই (Optimistic UI) আপডেট অথবা সেশন ট্র্যাকিং দিয়ে Read-Your-Writes নিশ্চিত করব যাতে সে নিজের একশনে কোনো অসঙ্গতি না দেখে।"

---

## 📝 Practice Questions

```markdown
### Basic Practice Questions
1. Linearizability এবং Eventual Consistency-এর মূল পার্থক্য কী?
2. Read-Your-Writes consistency কেন ইউজার এক্সপেরিয়েন্সের জন্য অত্যন্ত গুরুত্বপূর্ণ?
3. Cassandra-তে N=3 হলে কোন কোন R ও W কনফিগারেশন Strong Consistency দেবে?
4. Replication Lag কী এবং এটি কীভাবে Consistency নষ্ট করে?
5. Monotonic Reads গ্যারান্টি না থাকলে ব্যবহারকারী ব্রাউজারে কী অভিজ্ঞতা দেখতে পায়?

### Intermediate Practice Questions
6. Master-Replica ডাটাবেজ আর্কিটেকচারে কীভাবে Read-Your-Writes কনসিস্টেন্সি নিশ্চিত করবেন?
7. Eventual Consistency সিস্টেমে দুটি কনকারেন্ট রাইটের কনফ্লিক্ট কীভাবে সমাধান করা হয় (LWW বনাম CRDT)?
8. Causal Consistency কীভাবে ভেক্টর ক্লক (Vector Clocks) ব্যবহার করে কার্যকারণ সম্পর্ক রক্ষা করে?
9. Quorum রিডের সময় যদি একাধিক নোড ভিন্ন ভিন্ন ডেটা রিটার্ন করে, ক্লায়েন্ট কীভাবে সঠিক ডেটা নির্বাচন করে এবং ব্যাকগ্রাউন্ডে কী ঘটে?
10. ACID ট্রানজ্যাকশনের 'C' (Consistency) এবং CAP থিওরেমের 'C' (Consistency)-এর মধ্যে তাত্ত্বিক পার্থক্য কী?

### Advanced / Staff-Level Questions
11. Google Spanner কীভাবে TrueTime API (GPS + Atomic Clocks) এবং Commit Wait মেকানিজম ব্যবহার করে গ্লোবালি Linearizable Consistency বজায় রাখে?
12. Multi-leader বা Multi-region ডাটাবেজে Causal Consistency বাস্তবায়নের মেমোরি এবং নেটওয়ার্ক মেটাডাটা ওভারহেড কীভাবে অপ্টিমাইজ করা যায়?
13. একটি ব্যাংকিং লেজার সিস্টেমে আপনি কি কখনো Eventual Consistency ব্যবহার করবেন? CQRS এবং Event Sourcing-এর মাধ্যমে কীভাবে ব্যালেন্স ভ্যালিডেশন এবং হাই থ্রুপুট একসাথে অর্জন করা যায়?
14. Hybrid Logical Clocks (HLC) কেন ফিজিক্যাল NTP ক্লক ড্র্রিফটের সীমাবদ্ধতা দূর করতে CockroachDB এবং MongoDB-তে ব্যবহৃত হয়?
15. Distributed Transactions-এ Read-Committed বনাম Snapshot Isolation বনাম Serializable Isolation-এর ক্ষেত্রে কনসিস্টেন্সি গ্যারান্টির তুলনামূলক বিশ্লেষণ করুন।
```

---

## 🔑 Answer Key & Self-Test Evaluation

<details>
<summary>👉 <b>Answer Key ও সমাধান দেখতে এখানে ক্লিক করুন</b></summary>

1. **Linearizability vs Eventual:** Linearizability-তে রাইট শেষ হওয়ার সাথে সাথে পরবর্তী সমস্ত রিড বৈশ্বিকভাবে লেটেস্ট ডাটা দিতে বাধ্য (গ্লোবাল টাইমলাইন)। Eventual-এ সময়ের ব্যবধানে নোডগুলো সিঙ্ক হয়, মাঝের সময়ে স্টেল ডেটা রিড হতে পারে।
2. **Read-Your-Writes গুরুত্ব:** ইউজার কিছু সাবমিট করার পর রিফ্রেশ দিলে যদি তা না দেখে, তবে সে মনে করে সিস্টেম ক্র্যাশ করেছে এবং বারবার সাবমিট করে ডুপ্লিকেট তৈরি করে।
3. **Quorum Config:** $R + W > N$ সূত্র অনুযায়ী, $N=3$ হলে: (W=2, R=2), (W=3, R=1), বা (W=1, R=3) স্ট্রং কনসিস্টেন্সি দেবে।
4. **Replication Lag:** প্রাইমারি নোডে রাইট হওয়ার পর নেটওয়ার্কের মাধ্যমে রেপ্লিকায় ডাটা কপি হতে যে সময় ব্যয় হয়। এই সময়ে রেপ্লিকায় রিড করলে পুরোনো ডেটা পাওয়া যায়।
5. **Monotonic Reads Violation:** ইউজার পেজ রিফ্রেশ করলে একবার নতুন ডেটা দেখে পরের বার লোড ব্যালেন্সারের ভিন্ন রেপ্লিকায় গিয়ে পেছনের পুরোনো ডেটা দেখতে পায় (টাইম ট্রাভেল অ্যানোমালি)।
6. **Master-Replica Read-Your-Writes:** ইউজার রাইট করার পর নির্দিষ্ট সময়ের জন্য তার রিডগুলোকে মাস্টারে পিন করা (Sticky Master Routing) অথবা ক্লায়েন্ট সেশনে LSN/GTID সংরক্ষণ করে রেপ্লিকার সিঙ্ক ভ্যালিডেট করা।
7. **কনফ্লিক্ট রেজোলিউশন:** LWW (Last-Write-Wins) লেটেস্ট টাইমস্ট্যাম্প রেখে বাকিগুলো মুছে দেয়; CRDT (Conflict-free Replicated Data Type) গাণিতিক নিয়মে ডাটা কনভার্জ করে (যেমন কাউন্টার যোগ বা সেট ইউনিয়ন)।
8. **Vector Clocks:** প্রতিটি নোড নিজস্ব লজিক্যাল কাউন্টার রাখে। মেসেজ পাঠানোর সময় পুরো ভেক্টর পাঠানো হয়, ফলে কোন ইভেন্টটি কোনটির আগে ঘটেছে (Happens-Before) তা স্পষ্টভাবে ট্র্যাক করা যায়।
9. **Read Repair:** ক্লায়েন্ট সবচেয়ে বড় টাইমস্ট্যাম্প যুক্ত ডেটাকে ভ্যালিড হিসেবে গ্রহণ করে। একই সাথে ব্যাকগ্রাউন্ডে ডেটাবেজ পুরোনো ডেটা ধারণকারী নোডগুলোকে নতুন ডেটা দিয়ে প্যাচ করে দেয় (Read Repair)।
10. **ACID 'C' vs CAP 'C':** ACID-এর C মানে Database Constraints/Invariants অক্ষুণ্ণ রাখা (স্কিমা রুলস, ফরেন কি)। CAP-এর C হলো ডিস্ট্রিবিউটেড সিস্টেমের Linearizability (একক টাইমলাইনে লেটেস্ট রিড পাওয়া)।
11. **TrueTime Commit Wait:** ট্রানজ্যাকশনের সময় Spanner সময়সীমা $2\epsilon$ (সর্বোচ্চ ক্লক অনিশ্চয়তা, সাধারণত ৭ms) অপেক্ষা করে, যাতে নিশ্চিত হওয়া যায় বাস্তব সময়ের যেকোনো পরবর্তী ট্রানজ্যাকশন অবশ্যই আগের চেয়ে বড় টাইমস্ট্যাম্প পাবে।
12. **Causal Metadata Optimization:** এক্সপ্লিসিট ডিপেন্ডেন্সি গ্রাফ ব্যবহার না করে ক্লায়েন্ট সেশনে কেবল ভেক্টর টাইমস্ট্যাম্প সংরক্ষণ করা এবং ব্যাকগ্রাউন্ডে ডেল্টা-কম্প্রেশন প্রয়োগ করা।
13. **Banking with CQRS:** রাইট সাইডে স্ট্রংলি কনসিস্টেন্ট ACID লেজার রাখা যায় যা সমস্ত ট্রানজ্যাকশন লগ করে; রিড সাইডে Kafka দিয়ে প্রজেক্টেড Eventual Consistent ভিউ তৈরি করা যায় যা সাব-মিলিসেকেন্ডে ব্যালেন্স ডিসপ্লে করে।
14. **HLC:** ফিজিক্যাল ক্লক এবং ল্যামপোর্ট লজিক্যাল ক্লকের সংমিশ্রণ। এটি ফিজিক্যাল সময়ের কাছাকাছি থাকে এবং একই সাথে কনকারেন্ট ইভেন্টের ক্ষেত্রে লজিক্যাল ক্লক বাড়িয়ে কজুয়ালিটি বজায় রাখে।
15. **Isolation Levels:** Read-Committed ডার্টি রিড ঠেকায়; Snapshot Isolation ট্রানজ্যাকশনের শুরুর স্ন্যাপশট দেখায় (Phantom Read ঠেকায় কিন্তু Write Skew হতে পারে); Serializable সম্পূর্ণ লিনিয়ারাইজেবল ক্রম মেনে চলে।

</details>
