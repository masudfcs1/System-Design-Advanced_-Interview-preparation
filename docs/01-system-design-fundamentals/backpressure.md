# Backpressure (ব্যাকপ্রেশার)

[Back to System Design Fundamentals topics](README.md) · [Module guide](../01-system-design-fundamentals.md)

## 📌 Learning Checklist
- [ ] Understand the fundamental concept of Backpressure in distributed data pipelines.
- [ ] Grasp what happens when Producer Rate exceeds Consumer Rate (Unbounded Buffer -> OOM Crash).
- [ ] Contrast Backpressure mechanisms: Drop (Shedding), Buffer (Bounded Queuing), Block (Flow Control), and Pushback.
- [ ] Master Flow Control at different layers: TCP Window Size, Reactive Streams (Project Reactor / RxJava), and Kafka Pull Architecture.
- [ ] Analyze end-to-end backpressure propagation in IoT and event-driven architectures.
- [ ] Defend stream saturation and backpressure strategies in Staff-level interviews.

---

## 📖 Deep Dive Notes

### 1. সহজ সংজ্ঞা ও Intuitive Mental Model

ডিস্ট্রিবিউটেড ডাটা পাইপলাইন বা নেটওয়ার্ক কমিউনিকেশনে যখন কোনো **Producer** (তথ্য উৎপাদনকারী) যে গতিতে ডেটা তৈরি ও পাঠাতে থাকে, ডাউনস্ট্রিম **Consumer** (তথ্য গ্রহণকারী) যদি সেই একই গতিতে ডেটা প্রসেস করতে অক্ষম হয়—তখন কনজিউমার প্রডিউসারকে সিগন্যাল পাঠিয়ে ডেটা পাঠানোর গতি কমাতে বা সাময়িক স্থগিত করতে বাধ্য করার প্রতিক্রিয়াশীল মেকানিজমকে **Backpressure** বলে।

```
Producer (10,000 msg/sec) ═════════► [ Bounded Buffer ] ───► Consumer (1,000 msg/sec)
                                            ▲
                                  (Buffer 85% Full!)
                                            │
   ◄───── "SLOW DOWN!" (Backpressure Signal)┘
```

> 🧠 **Intuitive Mental Model (ফানেল বা পানি ঢালা অ্যানালজি):**
> আপনি একটি ছোট মুখের কাঁচের বোতলের ওপর একটি ফানেল বসিয়ে জগ থেকে দ্রুত পানি ঢালছেন।
> - ফানেলের নিচের ছিদ্রটি ছোট (**Consumer Processing Capacity**)।
> - আপনি যদি জগের পুরো পানি এক সেকেন্ডে ঢেলে দেন (**Fast Producer**), তবে ফানেল উপচে পানি বাইরে মেঝেতে পড়ে নোংরা হবে (**Buffer Overflow / Out of Memory Crash**)!
> - আপনি যখন দেখবেন ফানেল ভরে উঠছে, তখন আপনি নিজেই জগ থেকে পানি ঢালার গতি কমিয়ে দেবেন বা এক সেকেন্ড থামবেন (**Applying Backpressure**)। ফানেলের পানি নেমে গেলে আবার ঢালবেন।

---

### 2. The Unbounded Buffer Trap (কেন মেমোরি ক্র্যাশ করে?)

ব্যাকপ্রেশার না থাকলে সাধারণ ডেভেলপাররা কনজিউমারের মেমোরিতে একটি সাধারণ ইন-মেমোরি কিউ (যেমন: `java.util.concurrent.LinkedBlockingQueue`) তৈরি করে আনলিমিটেড ডেটা বাফার করতে থাকেন:
- প্রডিউসার পাঠাচ্ছে ১০,০০০ RPS।
- কনজিউমার প্রসেস করছে ১,০০০ RPS।
- প্রতি সেকেন্ডে ৯,০০০ মেসেজ মেমোরিতে জমতে থাকে।
- মাত্র ৫ মিনিটে মেমোরিতে ২৭ লাখ অবজেক্ট জমা হয়ে পুরো জেভিএম বা নোডজেস প্রসেস **OOM (Out Of Memory Error)** খেয়ে ক্র্যাশ করে!

#### সমাধান: ৪টি ব্যাকপ্রেশার স্ট্র্যাটেজি
1. **Block / Pushback:** বাফার ফুল হলে প্রডিউসার থ্রেডকে ব্লক করা (TCP Flow Control স্টাইল)।
2. **Buffer with Drop-Oldest / Drop-Latest:** বাফার নির্ধারিত সীমার বেশি হলে কম গুরুত্বপূর্ণ পুরনো ডেটা ফেলে দেওয়া (ভিডিও স্ট্রিমিং ও স্টক টিকারের জন্য উপযুক্ত)।
3. **Fail / Shed:** বাফার ফুল হলে নতুন রিকোয়েস্ট অবিলম্বে রিজেক্ট করে `HTTP 429` বা `503` এরর দেওয়া (লোড শেডিং)।
4. **Pull-based Architecture:** প্রডিউসার কনজিউমারের ওপর ডেটা পুশ না করে কনজিউমার যখন প্রস্তুত হবে কেবল তখনই ডেটা টেনে (Pull) আনবে (Apache Kafka-র দর্শন)।

---

### 3. Flow Control at Different Layers

```
Layer 4 (Transport Layer):
[ Client TCP Stack ] <── TCP Window Size = 0 (Window Full) ── [ Server TCP Stack ]
(Client stops transmitting packets over network wire)

Application / Stream Layer:
[ Reactive Publisher ] <── Subscription.request(10) ── [ Reactive Subscriber ]
(Publisher only sends exactly 10 items, no more)

Distributed Architecture Layer:
[ Kafka Broker Topic ] <── Consumer.poll(Duration) ── [ Worker Consumer Pool ]
(Workers pull messages on their own schedule and capacity)
```

1. **TCP Window Size (Layer 4):** রিসিভারের ওএস কার্নেল বাফার যখন ভরে যায়, সে টিসিপি এসিকে (ACK) প্যাকেটে `Window Size = 0` পাঠায়। সেন্ডারের ওএস কার্নেল সাথে সাথে ওয়্যারে নতুন প্যাকেট পাঠানো বন্ধ করে দেয়।
2. **Reactive Streams Specification (Java Flow / Project Reactor / RxJava):**
   - ডাইনামিক পুল মডেল।
   - সাবস্ক্রাইবার স্পষ্টভাবে কল করে: `subscription.request(n)`।
   - প্রডিউসার $n$-সংখ্যক ডেটা পাঠানোর পর হাত গুটিয়ে বসে থাকে যতক্ষণ না কনজিউমার পরবর্তী `request(m)` পাঠায়।
3. **Apache Kafka Pull Model:**
   - কাফকা ব্রোকার কনজিউমারের ওপর কোনো মেসেজ পুশ করে না।
   - কনজিউমার নিজস্ব গতিতে `poll()` মেথড কল করে ডিস্ক থেকে ডেটা পড়ে নেয়। কনজিউমার স্লো হলে কাফকা ডিস্কে মেসেজ জমতে থাকে (Consumer Lag বৃদ্ধি পায়), কিন্তু কনজিউমার কখনোই মেমোরি ক্র্যাশ করে না!

---

### 4. Alternatives & Strategies Comparison Matrix

| স্ট্র্যাটেজি | ডেটা লস ঝুঁকি? | মেমোরি ব্যবহার | জটিলতা | আদর্শ ব্যবহারের ক্ষেত্র |
|---|:---:|---|---|---|
| **Pull Architecture (Kafka)** | ❌ জিরো লস | নিয়ন্ত্রিত ও স্থির | মাঝারি | ডিস্ট্রিবিউটেড মেসেজিং ও ইটিএল (ETL) পাইপলাইন |
| **TCP Sliding Window** | ❌ জিরো লস | বাউন্ডেড কার্নেল বাফার | শূন্য (ওএস নেটিভ) | সকেট ও নেটওয়ার্ক কমিউনিকেশন |
| **Reactive Streams (RS)** | ❌ জিরো লস | সর্বনিম্ন | উচ্চ | সিঙ্গেল নোড নন-ব্লকিং মাইক্রোসার্ভিস |
| **Drop / Load Shedding** | ⚠️ ইচ্ছাকৃত লস | স্থির | কম | লাইভ ভিডিও/অডিও স্ট্রিমিং, আইওটি সেন্সর |

---

### 5. Failure Modes & Production Mitigations

#### Failure Mode 1: Head-of-Line Blocking in Reactive Streams
- **ঝুঁকি:** একটি স্ট্রিমের কনজিউমার দীর্ঘক্ষণ ব্লকিং কোনো কাজ করায় প্রডিউসারের আপস্ট্রিম বাফার আটকে থাকে এবং ওই পাইপলাইনের সাথে যুক্ত অন্যান্য দ্রুতগামী স্ট্রিমও আটকে যায়।
- **প্রতিরোধ (Mitigation):** প্রসেসিংকে আলাদা থ্রেড বা শিডিউলার পুল (`publishOn(Schedulers.boundedElastic())`)-এ আইসোলেট করা।

#### Failure Mode 2: Unmonitored Kafka Consumer Lag
- **ঝুঁকি:** কনজিউমার ব্যাকপ্রেশার সহ্য করছে ঠিকই কিন্তু প্রডিউসারের গতি এত বেশি যে কাফকার রিটেনশন পিরিয়ড (যেমন: ৭ দিন) শেষ হয়ে রিড করার আগেই মেসেজ ডিস্ক থেকে মুছে যায়।
- **প্রতিরোধ:** কনজিউমার ল্যাগ মেট্রিক্সে Prometheus/Grafana অ্যালার্ট রাখা এবং কুবারনেটিস KEDA দিয়ে কনজিউমার ল্যাগের ওপর ভিত্তি করে স্বয়ংক্রিয়ভাবে কনজিউমার পড স্কেল-আউট করা।

---

### 6. Senior / Staff Engineer Interview Defense

> **ইন্টারভিউয়ার:** *"একটি IoT প্ল্যাটফর্মে ১০ লাখ সেন্সর থেকে প্রতি সেকেন্ডে ডেটা আসছে। ব্যাকএন্ড ডাটাবেজ স্লো হয়ে গেলে আপনি কীভাবে এন্ড-টু-এন্ড ব্যাকপ্রেশার ম্যানেজ করবেন?"*
>
> 💡 **Staff-Level উত্তরের কাঠামো:**
> "১০ লাখ IoT ডিভাইসে সিঙ্ক্রোনাসলি ব্যাকপ্রেশার পাঠানো অসম্ভব, কারণ সেন্সরগুলোর কাছে অতিরিক্ত ডেটা বাফার করার মেমোরি থাকে না। আমাদের আর্কিটেকচার হবে একটি **Multi-Stage Buffered Backpressure Pipeline**:
> 1. **এন্ট্রি পয়েন্টে Kafka Buffer (Decoupling):** IoT গেটওয়েগুলো ইনকামিং ট্রাফিক কোনো প্রসেসিং ছাড়াই সরাসরি একটি ডিস্ট্রিবিউটেড ডিস্ক-বেসড মেসেজ ব্রোকারে (Apache Kafka) অ্যাপেন্ড করবে। কাফকা একটি স্থিতিস্থাপক শক-অ্যাবজরবার (Shock Absorber) হিসেবে কাজ করবে।
> 2. **Pull-based Consumer Processing:** ব্যাকএন্ড ডাটাবেজ রাইটাররা কাফকা থেকে **Pull Model**-এ ডেটা রিড করবে। ডাটাবেজ স্লো হলে কনজিউমাররা স্বয়ংক্রিয়ভাবে পোলিং রেট কমিয়ে দেবে—কনজিউমার কোনো মেমোরি ওওএম (OOM) ক্র্যাশের শিকার হবে না।
> 3. **Auto-Scaling with KEDA:** কনজিউমার ল্যাগ (Kafka Lag) বাড়তে শুরু করলে কুবারনেটিস ইভেন্ট-ড্রিভেন অটোস্কেলার (KEDA) অতিরিক্ত কনজিউমার পড চালু করে প্রসেসিং থ্রুপুট বাড়িয়ে ডাটাবেজের ধারণক্ষমতা অনুযায়ী ল্যাগ ক্লিয়ার করবে।
> 4. **এক্সট্রিম লোডে লেয়ার্ড ড্রপ পলিসি:** যদি কাফকা ক্লাস্টারের ডিস্কও ৯০% পূর্ণ হয়ে যায়, তবে IoT গেটওয়েতে অপ্রয়োজনীয় হাই-ফ্রিকোয়েন্সি ডুপ্লিকেট সেন্সর পিং (যেমন: প্রতি সেকেন্ডের টেম্পারেচার) ড্রপ করা হবে কিন্তু ক্রিটিক্যাল অ্যালার্ম মেসেজকে অগ্রাধিকার দিয়ে সুরক্ষিত রাখা হবে।"

---

## 📝 Practice Questions

```markdown
### Basic Practice Questions
1. Backpressure কী এবং এটি কেন পাইপলাইনের স্থায়িত্বের জন্য অপরিহার্য?
2. Producer যদি Consumer-এর চেয়ে দ্রুত ডেটা পাঠায় এবং কোনো ব্যাকপ্রেশার না থাকে, তবে কী বিপর্যয় ঘটে?
3. Unbounded Buffer কেন ডিস্ট্রিবিউটেড সিস্টেমে একটি অ্যান্টি-প্যাটার্ন?
4. Apache Kafka-র Pull Model কীভাবে প্রথাগত Push Model-এর চেয়ে উৎকৃষ্ট ব্যাকপ্রেশার সাপোর্ট দেয়?
5. TCP প্রোটোকলে কীভাবে Window Size ব্যবহার করে লেয়ার ৪-এ ব্যাকপ্রেশার নিশ্চিত করা হয়?

### Intermediate Practice Questions
6. Reactive Streams Specification-এ `Subscription.request(n)` মেথড কীভাবে নন-ব্লকিং ব্যাকপ্রেশার নিয়ন্ত্রণ করে?
7. Drop-Oldest এবং Drop-Latest ড্রপ স্ট্র্যাটেজির মধ্যে পার্থক্য কী এবং কোন ক্ষেত্রে কোনটি উপযুক্ত?
8. Apache Kafka-তে Consumer Lag কীভাবে মনিটর করবেন এবং এটি ব্যাকপ্রেশারের সাথে কীভাবে সম্পর্কিত?
9. Node.js Streams-এ `stream.pipe()` মেথড কীভাবে ইন্টারনাল বাফার সাইজ (`highWaterMark`) ব্যবহার করে ব্যাকপ্রেশার পরিচালনা করে?
10. HTTP-ভিত্তিক সিস্টেমে এপিআই গেটওয়ে ব্যাকএন্ডের ব্যাকপ্রেশার সিগন্যাল পেয়ে ক্লায়েন্টকে কীভাবে রেসপন্স জানায়?

### Advanced / Staff-Level Questions
11. কোটি কোটি IoT ডিভাইসের এন্ড-টু-এন্ড পাইপলাইনে (Device -> MQTT -> Gateway -> Kafka -> Flink -> Database) ধাপে ধাপে ব্যাকপ্রেশার প্রপাগেশন কীভাবে ডিজাইন করবেন?
12. Kubernetes Event-driven Autoscaling (KEDA) কীভাবে কাফকা কনজিউমার ল্যাগের ফিডব্যাক মেট্রিক্স ব্যবহার করে ডাইনামিক ব্যাকপ্রেশার প্রশমিত করে?
13. Apache Flink-এর Credit-based Flow Control মেকানিজম কীভাবে ডিস্ট্রিবিউটেড ক্লাস্টারের টাস্ক ম্যানেজারদের মধ্যে নেটওয়ার্ক বাফার ব্লকিং প্রতিরোধ করে?
14. রিঅ্যাক্টিভ সিস্টেমে (Project Reactor) যখন একটি ফাস্ট ফ্লাক্স (Flux) একটি স্লো ডাটাবেজ সাবস্ক্রাইবারকে ফিড করে, তখন `onBackpressureBuffer()`, `onBackpressureDrop()` এবং `limitRate()` অপারেটরগুলোর আর্কিটেকচারাল নির্বাচন কীভাবে করবেন?
15. ব্যাংকিং সিস্টেমে কোনো অবস্থাতেই ডেটা ড্রপ করা গ্রহণযোগ্য নয়—এমন জিরো-লস এনভায়রনমেন্টে চরম স্যাচুরেশন এলে ব্যাকপ্রেশার কীভাবে ট্রানজ্যাকশন থ্রোটলিং এবং এসএলএ রক্ষা করে?
```

---

## 🔑 Answer Key & Self-Test Evaluation

<details>
<summary>👉 <b>Answer Key ও সমাধান দেখতে এখানে ক্লিক করুন</b></summary>

1. **সংজ্ঞা:** কনজিউমার অতিরিক্ত লোডে আক্রান্ত হলে প্রডিউসারকে ডেটা ট্রান্সমিশনের গতি কমাতে বা থামাতে সংকেত পাঠানোর মেকানিজম।
2. **ব্যাকপ্রেশার ছাড়া বিপর্যয়:** মেমোরিতে অতিরিক্ত মেসেজ বাফার হতে হতে প্রসেস Out-of-Memory (OOM) খেয়ে ক্র্যাশ করে এবং সমস্ত ডেটা হারিয়ে যায়।
3. **Unbounded Buffer:** মেমোরির কোনো সীমা না থাকায় রিকোয়েস্ট কিউ অসীমভাবে বাড়ে, যা ওওএম ক্র্যাশ এবং দীর্ঘ ল্যাটেন্সি ঘটায়।
4. **Kafka Pull Model:** কাফকা কখনোই কনজিউমারে পুশ করে না; কনজিউমার তার নিজস্ব মেমোরি ও সক্ষমতা অনুযায়ী ব্যাচে ডেটা টেনে নেয়, ফলে সে কখনোই ওভারলোড হয় না।
5. **TCP Window Control:** রিসিভারের কার্নেল বাফার ফুল হলে সে ACKs প্যাকেটে উইন্ডো সাইজ শূন্য জানায়; সেন্ডার প্যাকেট পাঠানো অবিলম্বে বন্ধ করে।
6. **Reactive Streams Request(n):** কনজিউমার প্রডিউসারকে জানায় সে ঠিক কতটি ($n$) আইটেম গ্রহণ করতে প্রস্তুত। প্রডিউসার এর অতিরিক্ত একটি আইটেমও পাঠাতে পারে না।
7. **Drop-Oldest vs Drop-Latest:** Drop-Oldest পুরোনো ডেটা মুছে লেটেস্ট ডেটা রাখে (স্টক প্রাইস বা লাইভ গেমে উপযুক্ত); Drop-Latest নতুন ডেটা রিজেক্ট করে পূর্ববর্তী কিউ রক্ষা করে।
8. **Kafka Lag:** প্রডিউসার অফসেট এবং কনজিউমার অফসেটের মধ্যকার পার্থক্য। ল্যাগ ক্রমাগত বাড়ার অর্থ কনজিউমার প্রডিউসারের স্পিড ম্যাচ করতে পারছে না (ব্যাকপ্রেশার সিগন্যাল)।
9. **Node.js highWaterMark:** বাফার মেমোরি লিমিট (`highWaterMark`) ছুঁয়ে গেলে `write()` মেথড `false` রিটার্ন করে। ড্রেন ইভেন্ট না আসা পর্যন্ত সেন্ডার পজ থাকে।
10. **HTTP Backpressure:** গেটওয়ে ব্যাকএন্ড কিউ ফুল দেখলে ক্লায়েন্টকে `HTTP 429 Too Many Requests` বা `503 Service Unavailable` সাথে `Retry-After` দিয়ে ট্রাফিক কমায়।
11. **IoT Multi-stage Pipeline:** সেন্সরে ড্রপ পলিসি, গেটওয়েতে লোকাল বাফার, কাফকাতে ডিস্ক বাফারিং, এবং কনজিউমারে পুল-মডেল সমন্বয় করা।
12. **KEDA Scaling:** কাফকার অফসেট ল্যাগ ট্র্যাক করে কুবারনেটিস কনজিউমার পডের সংখ্যা ১ থেকে বাড়িয়ে ৫০ করে, ফলে অতিরিক্ত কনজিউমার দ্রুত বাফার খালি করে ফেলে।
13. **Flink Credit-based:** ডাউনস্ট্রিম টাস্ক আপস্ট্রিমকে ক্রেডিট (ফাঁকা বাফার সংখ্যা) বরাদ্দ করে। ক্রেডিট না থাকলে কোনো নেটওয়ার্ক প্যাকেট পাঠানো হয় না।
14. **Reactor Operators:** `limitRate(n)` প্রডিউসার থেকে ছোট ব্যাচে ডেটা আনে; `onBackpressureDrop()` অতিরিক্ত রিকোয়েস্ট ফেলে দেয়; `onBackpressureBuffer(max)` বাউন্ডেড মেমোরিতে আটকে রাখে।
15. **Zero-loss Banking:** মেমোরি বাফারিং বাদ দিয়ে এসিনক্রোনাস ডিস্ক লগিং (Kafka/RDBMS WAL) করা এবং এপিআই লেয়ারে নতুন ইনকামিং রিকোয়েস্টকে থ্রোটল করে সাময়িক ব্যাকঅফ করতে বাধ্য করা।

</details>
