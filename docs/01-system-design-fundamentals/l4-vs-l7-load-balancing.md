# Layer 4 vs Layer 7 Load Balancing

[Back to System Design Fundamentals topics](README.md) · [Module guide](../01-system-design-fundamentals.md)

## 📌 Learning Checklist
- [ ] Understand OSI Model Layers: Transport Layer (Layer 4) vs Application Layer (Layer 7).
- [ ] Contrast routing decision metrics: IP/Port (L4) vs HTTP Headers/Paths/Cookies/JSON (L7).
- [ ] Analyze TCP Handshake termination and connection bridging in L4 vs L7.
- [ ] Compare throughput, CPU utilization, and latency profiles (AWS NLB vs ALB).
- [ ] Understand hybrid architecture: Two-tier load balancing (L4 in front of L7).
- [ ] Defend L4 vs L7 architectural decisions in Staff-level interviews.

---

## 📖 Deep Dive Notes

### 1. সহজ সংজ্ঞা ও Intuitive Mental Model

কম্পিউটার নেটওয়ার্কিংয়ের ওএসআই (OSI - Open Systems Interconnection) মডেলের কোন স্তরে ট্রাফিক পরীক্ষা ও রাউটিং সিদ্ধান্ত নেওয়া হচ্ছে তার ওপর ভিত্তি করে লোড ব্যালেন্সারকে প্রধানত দুটি শ্রেণীতে ভাগ করা হয়: **Layer 4 (Transport Layer)** এবং **Layer 7 (Application Layer)**।

```
Layer 4 Load Balancing (Transport Layer):
[ Client ] ─── TCP SYN/ACK (IP + Port) ───> [ L4 Load Balancer ] ─── Forward Stream ───> [ Server ]
(প্যাকেটের ভেতরের কোনো ডেটা, URL বা HTTP হেডার খোলে না)

Layer 7 Load Balancing (Application Layer):
[ Client ] ─── Full HTTP/TLS Handshake ───> [ L7 Load Balancer ]
                                            (Decrypts SSL, Inspects /api/v1/orders)
                                                    │
                                                    ▼
                                            [ Specific Microservice Pod ]
```

> 🧠 **Intuitive Mental Model (ডাকপিয়ন বনাম অফিসের সেক্রেটারি):**
> - **Layer 4 (ডাকপিয়ন / কুরিয়ার ডেলিভারিম্যান):** কুরিয়ার কর্মী চিঠির খামের ভেতরের কোনো চিঠি বা টেক্সট পড়ে দেখে না (**No payload inspection**)। সে শুধু খামের ওপর লেখা প্রাপকের বাড়ির ঠিকানা ও ফ্ল্যাট নম্বর দেখে (**IP Address & TCP Port**), এবং সোজা সেখানে ডেলিভারি দিয়ে চলে আসে। কাজটি অবিশ্বাস্য রকমের দ্রুত এবং এতে খুব কম মেধা বা প্রচেষ্টা ব্যয় হয় (**Ultra-high throughput, Low CPU**)।
> - **Layer 7 (অফিসের প্রধান সেক্রেটারি / অভ্যর্থনাকারী):** সেক্রেটারি প্রতিটি চিঠির খাম খুলে ভেতরের চিঠিটি পড়ে দেখেন (**Inspects HTTP Headers & Payload**)। যদি চিঠিটি "বিলিং ডিপার্টমেন্ট"-এর হয়, তবে তিনি সেটি একাউন্টস ম্যানেজারের টেবিলে পাঠান; আর যদি "চাকরির আবেদন" হয়, তবে তিনি এইচআর ডিপার্টমেন্টে পাঠান (**Content-based / Path-based routing**)। কাজটি অত্যন্ত বুদ্ধিমত্তাপূর্ণ, কিন্তু প্রতিটি চিঠি খুলে পড়ার কারণে তার সময় ও শ্রম বেশি লাগে (**Higher CPU, slightly higher latency**)।

---

### 2. Deep Dive: Layer 4 Load Balancing

- **রাউটিং ডেটা:** ক্লায়েন্ট আইপি, ডেস্টিনেশন আইপি, টিসিপি/ইউডিপি পোর্ট নম্বর।
- **টিসিপি সংযোগ পরিচালনা:** বেশিরভাগ L4 লোড ব্যালেন্সার প্যাকেট লেভেলে NAT (Network Address Translation) চালায় অথবা স্টেটফুল টিসিপি সেশন ম্যাপ করে। কোনো টিসিপি স্ট্রিম পুনরায় তৈরি করতে হয় না।
- **সুবিধা:**
  1. অত্যন্ত উচ্চ থ্রুপুট—প্রতি সেকেন্ডে কয়েক মিলিয়ন কনকারেন্ট কানেকশন হ্যান্ডেল করতে পারে।
  2. প্রায় শূন্য প্রসেসিং ল্যাটেন্সি ($<1$ মিলিসেকেন্ড)।
  3. প্রোটোকল অজ্ঞেয়বাদী (Protocol Agnostic)—যেসব প্রোটোকল HTTP নয় (যেমন: ডাটাবেজ ট্রাফিক, গেমের UDP প্যাকেট, কাফকা ব্রোকার কানেকশন, SMTP ইমেইল), সেগুলোর জন্য L4 একমাত্র উপায়।
- **বাস্তব উদাহরণ:** AWS Network Load Balancer (NLB), Linux IPVS (IP Virtual Server), HAProxy TCP Mode।

---

### 3. Deep Dive: Layer 7 Load Balancing

- **রাউটিং ডেটা:** সম্পূর্ণ ডিক্রিপ্টেড অ্যাপ্লিকেশন পে-লোড—HTTP URL Path (`/api/v1/checkout` বনাম `/static/images`), HTTP Headers (`Host`, `User-Agent`), Cookies (Session Affinity), HTTP Methods (`GET` vs `POST`), অথবা gRPC মেথড।
- **টিসিপি সংযোগ পরিচালনা:** L7 প্রক্সিকে ক্লায়েন্টের সাথে একটি সম্পূর্ণ আলাদা TCP ও TLS হ্যান্ডশেক সম্পন্ন করতে হয়, ডেটা ডিক্রিপ্ট করতে হয়, তারপর উপযুক্ত ব্যাকএন্ড নোডের সাথে নতুন কানেকশনে ডেটা পাঠাতে হয় (Two separate TCP connections)।
- **সুবিধা:**
  1. **Smart Routing:** একটি একক ডোমেইনের ট্রাফিক বিভিন্ন সার্ভিসে নিখুঁতভাবে রাউট করা যায়।
  2. **SSL Termination & Caching:** ক্লায়েন্টের জন্য এনক্রিপশন হ্যান্ডেল করা এবং স্ট্যাটিক এসেট ক্যাশ করা।
  3. **Security (WAF):** ক্রস-সাইট স্ক্রিপ্টিং (XSS) বা এসকিউএল ইনজেকশন পে-লোড স্ক্যান করে আটকানো।
- **বাস্তব উদাহরণ:** AWS Application Load Balancer (ALB), Nginx HTTP Mode, Envoy Proxy, Traefik।

---

### 4. Alternatives & Architectural Comparison Matrix

| প্যারামিটার | Layer 4 Load Balancer (NLB) | Layer 7 Load Balancer (ALB) |
|---|---|---|
| **OSI স্তর** | Layer 4 (Transport: TCP/UDP) | Layer 7 (Application: HTTP/HTTPS/gRPC) |
| **রাউটিং বিবেচনা** | কেবল IP Address ও Port | URL Path, Host Header, Cookie, Query String |
| **থ্রুপুট সক্ষমতা** | চরম উচ্চ (লাখ লাখ থেকে কোটি RPS) | মাঝারি-উচ্চ (হাজার থেকে লাখ RPS) |
| **রিসোর্স ব্যবহার** | অতি নগণ্য CPU ও RAM | উচ্চ CPU (SSL Decryption ও প্যাকেট পার্সিং) |
| **ল্যাটেন্সি ওভারহেড** | সাব-মিলিসেকেন্ড ($<0.5$ ms) | ২ থেকে ১০ মিলিসেকেন্ড |
| **SSL/TLS Termination** | পাস-থ্রু (এনক্রিপ্টেড থাকে) বা বেসিক TLS | পূর্ণাঙ্গ সার্টিফিকেট ম্যানেজমেন্ট ও ডিক্রিপশন |
| **নন-HTTP ট্রাফিক সাপোর্ট** | চমৎকার (MySQL, Redis, DNS, UDP Streams) | কাজ করে না (শুধু HTTP/1.1, HTTP/2, gRPC, WS) |

---

### 5. Production Pattern: Two-Tier Load Balancing Architecture

আধুনিক আল্ট্রা-স্কেল ক্লাউড প্ল্যাটফর্মগুলোতে (যেমন Netflix, Uber, বা Amazon) L4 এবং L7-এর মধ্যে কোনো একটিকে বেছে না নিয়ে **হাইব্রিড টু-টায়ার (Two-Tier) আর্কিটেকচার** প্রয়োগ করা হয়:

```
[ Internet Traffic: Millions of Connections ]
                     │
                     ▼
       ┌───────────────────────────┐
       │   Tier 1: Layer 4 NLB     │  <-- Ultra-high throughput, Static Anycast IPs,
       │   (AWS NLB / Katran eBPF) │      DDoS Absorption, Wire-speed distribution
       └─────────────┬─────────────┘
                     │
         ┌───────────┴───────────┐
         ▼                       ▼
   ┌───────────┐           ┌───────────┐
   │ Tier 2:   │           │ Tier 2:   │  <-- Smart Layer 7 Routing, SSL Decryption,
   │ L7 Envoy  │           │ L7 Envoy  │      Auth, Path routing, Rate limiting
   └─────┬─────┘           └─────┬─────┘
         │                       │
         └───────────┬───────────┘
                     ▼
         [ Backend Microservices ]
```

---

### 6. Senior / Staff Engineer Interview Defense

> **ইন্টারভিউয়ার:** *"আমাদের সিস্টেমে কোটি কোটি রিকোয়েস্ট আসছে। আমরা কি শুধু L7 লোড ব্যালেন্সার (ALB) ব্যবহার করব, নাকি L4 (NLB) বেছে নেব?"*
>
> 💡 **Staff-Level উত্তরের কাঠামো:**
> "এটি আমাদের অ্যাপ্লিকেশন প্রোটোকল এবং ট্রাফিকের প্রকৃতির ওপর নির্ভর করবে:
> 1. **যদি একক স্তর বেছে নিতে হয়:**
>    - আমাদের যদি **স্মার্ট পাথ রাউটিং** (`/orders` বনাম `/payments`), SSL টার্মিনেশন এবং হেডার-বেসড সিকিউরিটির দরকার হয় এবং ট্রাফিক সাধারণ এন্টারপ্রাইজ স্কেলে থাকে, তবে **Layer 7 (ALB)** যথেষ্ট।
>    - আমাদের সার্ভিস যদি **নন-HTTP হয়** (যেমন মাল্টিপ্লেয়ার গেমের UDP সকেট, কাফকা ব্রোকার ক্লাস্টার, বা ডাটাবেজ শার্ড) অথবা ট্রাফিকের ভলিউম যদি এত বিশাল হয় যে L7-এর প্রসেসিং কস্ট ও ল্যাটেন্সি অগ্রহণযোগ্য, তবে আমরা নির্দ্বিধায় **Layer 4 (NLB)** বেছে নেব।
> 2. **Staff-Level গোল্ডেন আর্কিটেকচার (Two-Tier Model):** কোটি কোটি রিকোয়েস্টের এক্সট্রিম স্কেলে আমরা ফ্রন্ট ডোরে একটি **Layer 4 লোড ব্যালেন্সার** রাখব যা স্ট্যাটিক আইপিতে কোনো ডিক্রিপশন ছাড়াই ওয়্যার-স্পিডে ট্রাফিক স্প্লিট করবে। তার পেছনে অটো-স্কেলিং গ্রুপে একগুচ্ছ **Layer 7 Envoy প্রক্সি** রাখব যা পাথ-রাউটিং ও অথেনটিকেশন হ্যান্ডেল করে অভ্যন্তরীণ পডগুলোতে ট্রাফিক পাঠাবে।"

---

## 📝 Practice Questions

```markdown
### Basic Practice Questions
1. OSI মডেলের কোন স্তরে Layer 4 এবং Layer 7 লোড ব্যালেন্সার কাজ করে?
2. Layer 4 লোড ব্যালেন্সার কোন তথ্যের ওপর ভিত্তি করে প্যাকেট ফরোয়ার্ড করে?
3. Layer 7 লোড ব্যালেন্সার কোন কোন তথ্যের (যেমন URL, Cookie) ওপর ভিত্তি করে রাউটিং করে?
4. কেন L4 লোড ব্যালেন্সার L7-এর চেয়ে অনেক বেশি থ্রুপুট এবং কম ল্যাটেন্সি দেয়?
5. একটি ডাটাবেজ ক্লাস্টার (MySQL/PostgreSQL) লোড ব্যালেন্স করতে L4 না L7 ব্যবহার করবেন?

### Intermediate Practice Questions
6. TCP Termination কী এবং কীভাবে L7 লোড ব্যালেন্সার দুটি আলাদা TCP কানেকশন হ্যান্ডেল করে?
7. Path-based Routing এবং Host-based Routing কীভাবে L7 লোড ব্যালেন্সারে কনফিগার করা হয়?
8. Direct Server Return (DSR) আর্কিটেকচার কি L7 লোড ব্যালেন্সারে সম্ভব? কেন বা কেন নয়?
9. SSL Passthrough বনাম SSL Offloading (Termination)-এর মধ্যে পার্থক্য কী এবং L4-এ কীভাবে এন্ড-টু-এন্ড এনক্রিপশন বজায় রাখা যায়?
10. gRPC ট্রাফিক লোড ব্যালেন্স করতে সাধারণ L4 লোড ব্যালেন্সার ব্যবহার করলে কেন সমস্ত ট্রাফিক একটিমাত্র সার্ভারেই চলে যায়?

### Advanced / Staff-Level Questions
11. gRPC-তে HTTP/2 মাল্টিপ্লেক্সিংয়ের কারণে L4 লোড ব্যালেন্সারের কার্যকারিতা নষ্ট হয়—এই সমস্যা সমাধানে Envoy বা L7 ব্যালেন্সার কীভাবে স্ট্রিম-লেভেল ব্যালেন্সিং করে?
12. আল্ট্রা-হাই স্কেলে Two-Tier লোড ব্যালেন্সিং (L4 NLB-এর পেছনে L7 ALB ফ্লিট) ডিজাইন করার সময় হেলথ চেক এবং কানেকশন ড্রেনিং কীভাবে সিঙ্ক করবেন?
13. Meta-র Katran লোড ব্যালেন্সার কীভাবে Linux eBPF এবং XDP (eXpress Data Path) ব্যবহার করে ৪ নম্বর লেয়ারে মেমোরি কপি ছাড়াই প্যাকেট সুইচ করে?
14. L4 লোড ব্যালেন্সার ব্যবহার করার সময় ব্যাকএন্ড সার্ভারে ক্লায়েন্টের আসল আইপি পাস করতে PROXY Protocol (HAProxy PROXY protocol v1/v2) কীভাবে কাজ করে?
15. DDoS আক্রমণের সময় SYN Flood এবং UDP Amplification আক্রমণ প্রতিরোধে L4 ও L7 লোড ব্যালেন্সারের যৌথ প্রতিরোধ কৌশল কী?
```

---

## 🔑 Answer Key & Self-Test Evaluation

<details>
<summary>👉 <b>Answer Key ও সমাধান দেখতে এখানে ক্লিক করুন</b></summary>

1. **OSI স্তর:** Layer 4 হলো Transport Layer (TCP/UDP); Layer 7 হলো Application Layer (HTTP/HTTPS/gRPC)।
2. **L4 রাউটিং মেট্রিক্স:** সোর্স আইপি, ডেস্টিনেশন আইপি, সোর্স পোর্ট এবং ডেস্টিনেশন পোর্ট (4-tuple)।
3. **L7 রাউটিং মেট্রিক্স:** HTTP পাথ (`/users`), হোস্টনেম (`api.com`), হেডারস (`Authorization`), কুকি এবং কুয়েরি স্ট্রিং।
4. **L4 দ্রুত কেন:** এটি পে-লোড ডিক্রিপ্ট করে না, হেডার পার্স করে না। শুধু কার্নেল লেভেলে প্যাকেট ফরওয়ার্ড করায় সিপিইউ খরচ প্রায় শূন্য থাকে।
5. **ডাটাবেজ লোড ব্যালেন্সিং:** Layer 4 (NLB/HAProxy TCP mode); কারণ ডাটাবেজের যোগাযোগ কোনো HTTP প্রোটোকল নয়, এটি পিওর বাইনারি টিসিপি কানেকশন।
6. **TCP Termination:** L7-এ ক্লায়েন্টের সাথে হ্যান্ডশেক শেষ করে সম্পূর্ণ পে-লোড গ্রহণ করা হয়, তারপর নতুন সকেটে ব্যাকএন্ডে পাঠানো হয়। L4 কেবল প্যাকেট স্ট্রিম পাস করে।
7. **Path & Host Routing:** হোস্ট হেডার দেখে ভিন্ন ভিন্ন ডোমেন এবং ইউআরএল পাথ দেখে ভিন্ন ভিন্ন মাইক্রোসার্ভিসে রিকোয়েস্ট ভাগ করে দেওয়া।
8. **DSR in L7:** L7-এ DSR সম্ভব নয়, কারণ লোড ব্যালেন্সারকে রেসপন্স হেডার ও কুকি মডিফাই করতে ক্লায়েন্ট ও সার্ভারের ট্রাফিকের মাঝে থাকতে হয়। DSR কেবল L4-এ সম্ভব।
9. **SSL Passthrough vs Offloading:** অফলোডিংয়ে প্রক্সি ডিক্রিপ্ট করে আন-এনক্রিপ্টেড পাঠায়; পাস-থ্রুতে L4 এনক্রিপ্টেড প্যাকেট অপরিবর্তিত ব্যাকএন্ডে পাঠিয়ে দেয় যেখানে ব্যাকএন্ড নিজস্ব কী দিয়ে ডিক্রিপ্ট করে।
10. **gRPC L4 Issue:** gRPC একটি লং-লিভড টিসিপি কানেকশনের ভেতর দিয়ে সমস্ত স্ট্রিম মাল্টিপ্লেক্স করে। L4 কেবল কানেকশন চেনে, ফলে কোটি কোটি আরপিসি কল একটিমাত্র কানেকশন দিয়ে একটিমাত্র পডেই চলে যায়।
11. **gRPC L7 Balancing:** L7 ব্যালেন্সার HTTP/2 ফ্রেম ডিকোড করতে পারে। ফলে একই টিসিপি কানেকশনের ভেতরের বিভিন্ন আরপিসি স্ট্রিমকে আলাদা আলাদা ব্যাকএন্ড পডে সুষমভাবে ডিস্ট্রিবিউট করে।
12. **Two-Tier Health Check:** L4 ব্যালেন্সার L7 প্রক্সির হেলথ দেখে; আর L7 প্রক্সি ব্যাকএন্ড মাইক্রোসার্ভিসের হেলথ দেখে। L7 কোনো নোড ড্রপ করলে সাথে সাথে অভ্যন্তরীণ রুট থেকে সরিয়ে দেয়।
13. **Katran eBPF:** নেটওয়ার্ক কার্ডে প্যাকেট আসার সাথে সাথে ওএস কার্নেল বাইপাস করে সরাসরি XDP হুকে প্যাকেট ইন্সপেক্ট করে দ্রুত ফরোয়ার্ড করে, কোনো কনটেক্সট সুইচ লাগে না।
14. **PROXY Protocol:** L4-এ যেহেতু HTTP হেডার যোগ করা যায় না, তাই টিসিপি পেলোডের শুরুতে ছোট একটি প্রিফিক্স হেডার (যেমন `PROXY TCP4 client_ip dest_ip ...`) ইনজেক্ট করে আসল আইপি জানিয়ে দেওয়া হয়।
15. **DDoS Defense:** L4 লেভেলে SYN Proxy দিয়ে SYN Flood এবং UDP স্পুফিং এজেই ড্রপ করা; আর L7 লেভেলে WAF ও রেট লিমিটিং দিয়ে স্লোরিস ও HTTP ফ্লাড প্রতিরোধ করা।

</details>
