# gRPC (gRPC Remote Procedure Calls)

[Back to Networking topics](README.md) · [Module guide](../02-networking.md)

## 📌 Learning Checklist
- [ ] Understand the architecture of gRPC and how it abstracts network calls into local function calls.
- [ ] Master Protocol Buffers (Protobuf v3): IDL definition, binary serialization, and backward compatibility.
- [ ] Understand HTTP/2 as the underlying transport: Multiplexing, Binary framing, and HTTP Trailers.
- [ ] Learn the 4 RPC Streaming Modes: Unary, Server Streaming, Client Streaming, and Bi-directional Streaming.
- [ ] Analyze gRPC Deadlines, Context Propagation, and Error Model (Status Codes).
- [ ] Defend gRPC vs REST in microservice architectures and Staff-level system design interviews.

---

## 📖 Deep Dive Notes

### 1. সহজ সংজ্ঞা ও Intuitive Mental Model

**gRPC** হলো গুগল কর্তৃক ২০১৫ সালে ওপেন-সোর্সকৃত একটি আধুনিক, অত্যন্ত উচ্চগতির, এবং ক্লাউড-নেটিভ **Remote Procedure Call (RPC)** ফ্রেমওয়ার্ক। 

এটি ডেভেলপারকে এমন একটি অভিজ্ঞতা দেয় যেন রিমোট সার্ভারে অবস্থিত কোনো ফাংশন বা মেথডকে ঠিক নিজের লোকাল মেশিনের সাধারণ ইন-মেমোরি ফাংশন কলের মতো নির্বিঘ্নে কল করা যায়—মাঝের নেটওয়ার্ক সিরিয়ালাইজেশন, ট্রান্সপোর্ট এবং এনকোডিংয়ের সমস্ত জটিলতা ফ্রেমওয়ার্ক স্বয়ংক্রিয়ভাবে আড়াল করে রাখে।

```
[ Client Machine (Go) ]                          [ Server Machine (Java) ]
┌─────────────────────────┐                      ┌─────────────────────────┐
│   client.GetOrder(123)  │                      │   func GetOrder(id) {   │
├─────────────────────────┤                      ├─────────────────────────┤
│   Auto-generated Stub   │                      │    Auto-generated Skel  │
├─────────────────────────┤                      ├─────────────────────────┤
│ Protobuf (Binary Encode)│                      │ Protobuf (Binary Decode)│
├─────────────────────────┤                      ├─────────────────────────┤
│      HTTP/2 Stream      │ ════ Network Wire ══►│      HTTP/2 Stream      │
└─────────────────────────┘                      └─────────────────────────┘
```

> 🧠 **Intuitive Mental Model (অনুবাদক সহ সরাসরি ফোন কল বনাম ডাকযোগে ফর্ম পূরণ):**
> - **REST over JSON (ডাকযোগে ফর্ম):** আপনাকে একটি লম্বা কাগজের ফর্ম ইংরেজিতে হাতে লিখে ডাক খামে ভরে পাঠাতে হয়। পোস্টম্যান চিঠি নিয়ে যায়। ওপারে প্রাপক বসে বসে ইংরেজি টেক্সট পড়ে পড়ে পার্স করে ডেটাবেজে লেখে। প্রচুর কালি, কাগজ ও প্রসেসিং সময় নষ্ট হয় (**Large JSON text payload, slow string parsing**)।
> - **gRPC over Protobuf (বিশেষায়িত বাইনারি সংকেত):** উভয় প্রান্তে দুটি অত্যন্ত দক্ষ ও প্রশিক্ষিত রোবট বসে আছে (**Generated Stubs**)। রোবট দুটি একটি সরাসরি ফাইবার ক্যাবলে যুক্ত (**HTTP/2 Multiplexing**)। তারা কোনো ইংরেজি বাক্য লেখে না; তারা কম্পিউটারের বিশুদ্ধ বাইনারি বিট কোডে (Protobuf) নিমেষের মধ্যে কয়েক মাইক্রোসেকেন্ডে ডেটা ছুঁড়ে মারে! এক প্রান্ত গো (Go) ল্যাঙ্গুয়েজে হলেও অন্য প্রান্তের জাভা (Java) রোবট নিমেষেই তা নিজের অবজেক্টে রূপান্তর করে নেয় (**Multi-language type safety**)।

---

### 2. Protocol Buffers (Protobuf v3): বাইনারি কমপ্যাক্টনেস

JSON একটি টেক্সট ফরম্যাট—প্রতিটি ফিল্ডের নাম (`"user_id": 12345`) প্রতি রিকোয়েস্টে স্ট্রিং হিসেবে ওয়্যারে যায়। 
Protobuf-এ কোনো ফিল্ডের নাম ওয়্যারে যায় না; কেবল একটি সংখ্যাসূচক ট্যাগ নম্বর (Tag Number) যায়:

```protobuf
syntax = "proto3";

package order;

message OrderRequest {
  int32 order_id = 1;      // ট্যাগ ১
  string user_email = 2;   // ট্যাগ ২
  bool is_priority = 3;    // ট্যাগ ৩
}

service OrderService {
  rpc GetOrder (OrderRequest) returns (OrderResponse);
}
```

#### কেন Protobuf JSON-এর চেয়ে ৫ থেকে ১০ গুণ দ্রুত এবং ছোট?
1. **Field Tags instead of Strings:** `"user_email"` লেখার বদলে ওয়্যারে মাত্র কয়েক বিটের বাইনারি ট্যাগ নম্বর `2` যায়।
2. **Varints (Variable-Length Quantities):** সাধারণ পূর্ণসংখ্যা (যেমন সংখ্যা ৫) পাঠাতে ৪ বা ৮ বাইট লাগে না; Varint এনকোডিংয়ে ছোট সংখ্যা মাত্র **১ বাইটে** স্টোর হয়।
3. **No String Parsing:** কম্পিউটারের সিপিইউ বাইনারি ডেটা সরাসরি মেমোরি রেজিস্টারে লোড করতে পারে—কোনো ব্যয়বহুল স্ট্রিং টোকেনাইজেশন বা এসকি ফ্রেম পার্সিং লাগে না।

---

### 3. The 4 Communication Modes of gRPC

```
1. Unary RPC (Traditional Request-Response):
Client ──────── Request ────────► Server
Client ◄─────── Response ─────── Server

2. Server Streaming (Single Request, Stream of Responses - e.g. Stock Tickers):
Client ──────── Request ────────► Server
Client ◄── Msg 1 ── Msg 2 ── Msg 3 (Stream) ── Server

3. Client Streaming (Stream of Requests, Single Response - e.g. File Upload):
Client ── Msg 1 ── Msg 2 ── Msg 3 (Stream) ──► Server
Client ◄─────── Response ─────── Server

4. Bi-directional Streaming (Both sides read/write simultaneously - e.g. Chat/Gaming):
Client ─── Stream of Msgs ───► Server
Client ◄── Stream of Msgs ─── Server
```

---

### 4. HTTP/2 Transport & HTTP Trailers

gRPC সাধারণ HTTP/1.1-এ কাজ করতে পারে না; এটি কঠোরভাবে **HTTP/2**-এর ওপর প্রতিষ্ঠিত:
- এটি একটি একক TCP সকেটে শত শত সমান্তরাল RPC কল মাল্টিপ্লেক্স করে।
- **HTTP Trailers-এর জাদু:** সাধারণ এপিআই-তে রেসপন্সের শুরুতেই স্ট্যাটাস কোড (`200 OK`) পাঠিয়ে দিতে হয়। কিন্তু একটি বিশাল স্ট্রিমিং কলের সময় রেসপন্সের মাঝপথে ডাটাবেজ ক্র্যাশ করলে কী হবে?
- gRPC বডির শেষে **HTTP Trailers** হিসেবে স্ট্যাটাস পাঠায় (`grpc-status: 0 (OK)` অথবা `grpc-status: 14 (UNAVAILABLE)`)। ফলে দীর্ঘমেয়াদী স্ট্রিমিং কলের শেষ প্রান্তেও নির্ভুল এরর রিপোর্টিং সম্ভব হয়!

---

### 5. Alternatives & Comparison Matrix

| মেট্রিক | gRPC | REST API | GraphQL |
|---|---|---|---|
| **ডেটা ফরম্যাট** | **বাইনারি (Protobuf)** | টেক্সট (JSON) | টেক্সট (JSON) |
| **ট্রান্সপোর্ট লেয়ার** | **HTTP/2 (বাধ্যতামূলক)** | HTTP/1.1 বা HTTP/2 | HTTP/1.1 বা HTTP/2 |
| **টাইপ সেফটি** | **কঠোরভাবে কম্পাইল-টাইম টাইপড** | সাধারণত রান-টাইম (ঐচ্ছিক TS/OpenAPI) | কঠোর স্কিমা (SDL) |
| **স্ট্রিমিং সক্ষমতা** | **ফুল বাইডাইরেকশনাল স্ট্রিমিং** | কেবল SSE (একমুখী) | সাবস্ক্রিপশন (WebSocket) |
| **ব্রাউজার সাপোর্ট** | ❌ দুর্বল (Envoy gRPC-Web প্রক্সি লাগে) | **১০০% নেটিভ ও সহজ** | চমৎকার |
| **আদর্শ ক্ষেত্র** | **ইন্টারনাল মাইক্রোসার্ভিস ব্যাকবোন** | পাবলিক API, থার্ড-পার্টি সার্ভিস | রিচ ওয়েব/মোবাইল ড্যাশবোর্ড |

---

### 6. Failure Modes & Production Mitigations

#### Failure Mode 1: Load Balancing Bottleneck on L4 Load Balancers
- **ঝুঁকি:** gRPC ক্লায়েন্ট একটি একক লং-লিভড HTTP/2 কানেকশন খুলে রাখে। সাধারণ Layer 4 লোড ব্যালেন্সার (AWS NLB) কানেকশন দেখে রাউট করে। ফলে কোটি কোটি gRPC রিকোয়েস্ট একটিমাত্র কানেকশন দিয়ে ক্লাস্টারের একটিমাত্র সার্ভার নোডেই চলে যায়, আর বাকি ৯৯টি সার্ভার অলস বসে থাকে!
- **প্রতিরোধ (Mitigation):** **Layer 7 Load Balancing (AWS ALB / Envoy Proxy)** অথবা **Client-side Load Balancing:** ক্লায়েন্ট নিজেই সার্ভিস ডিসকভারি (Consul/K8s) থেকে সমস্ত পড আইপি চেনে এবং স্ট্রিম-লেভেলে প্রতিটি RPC কল আলাদা আলাদা পডে ডিস্ট্রিবিউট করে।

---

### 7. Senior / Staff Engineer Interview Defense

> **ইন্টারভিউয়ার:** *"ইন্টারনাল মাইক্রোসার্ভিসে REST বাদ দিয়ে gRPC ব্যবহার করার মূল প্রযুক্তিগত সুবিধাগুলো কী? কেন এটি ১০ গুণ বেশি পারফরম্যান্স দেয়?"*
>
> 💡 **Staff-Level উত্তরের কাঠামো:**
> "মাইক্রোসার্ভিসের অভ্যন্তরীণ ব্যাকবোনে gRPC গ্রহণ করার পেছনে ৩টি মৌলিক স্থাপত্যিক কারণ রয়েছে:
> 1. **বাইনারি সিরিয়ালাইজেশন ও মেমোরি দক্ষতা:** JSON টেক্সট পার্সিং সিপিইউ-ইনটেনসিভ। Protobuf বাইনারি হওয়ায় এটি ডেটার সাইজ ৬০-৮০% কমিয়ে দেয় এবং সিপিইউ সিরিয়ালাইজেশন স্পিড ৫ থেকে ১০ গুণ বৃদ্ধি করে।
> 2. **HTTP/2 মাল্টিপ্লেক্সিং ওভারহেড এলিমিনেশন:** REST-এ প্রতি কলে নতুন টিসিপি হ্যান্ডশেক বা কানেকশন পুলিং ওভারহেড থাকে। gRPC একটি একক টিসিপি পাইপে হাজার হাজার কনকারেন্ট কল ইন্টারলিভ করে পাঠিয়ে থ্রেড ব্লকিং দূর করে।
> 3. **কঠোর টাইপ সেফটি ও পলিগ্লট কোড জেনারেশন:** প্রোটোবাইজড সিঙ্গেল সোর্স অফ ট্রুথ (IDL) থেকে গো, জাভা, পাইথন এবং সি++ এর জন্য অটোমেটিক টাইপড কোড জেনারেট হয়। কোনো ডেভেলপার ফিল্ডের নাম ভুল লিখলে কম্পাইল-টাইমেই এরর ধরে ফেলে—রানটাইমে কোনো 'undefined' বা ডাটা মিসম্যাচ বাগ ঘটতে পারে না।
> 4. **ফার্স্ট-ক্লাস ডেডলাইন প্রপাগেশন:** gRPC-তে বিল্ট-ইন ডেডলাইন প্রপাগেশন থাকে, যা মাইক্রোসার্ভিস ডিপ চেইনে কোনো রিকোয়েস্ট টাইমআউট হলে পেছনের সমস্ত অকেজো কম্পিউটিং অবিলম্বে ক্যানসেল করে দেয়।"

---

## 📝 Practice Questions

```markdown
### Basic Practice Questions
1. gRPC-এর পূর্ণরূপ কী এবং এটি সাধারণ REST API-র চেয়ে কোন মৌলিক দিক থেকে আলাদা?
2. Protocol Buffers (Protobuf) কী এবং এটি কীভাবে কাজ করে?
3. gRPC যোগাযোগের জন্য কোন ট্রান্সপোর্ট প্রোটোকল কঠোরভাবে ব্যবহার করে?
4. Protobuf-এ ফিল্ড ট্যাগ নম্বর (Field Numbers: 1, 2, 3...) কেন অপরিবর্তনীয় রাখা বাধ্যতামূলক?
5. gRPC-র ৪টি কমিউনিকেশন মোডের নাম বলুন।

### Intermediate Practice Questions
6. কেন সাধারণ Layer 4 (L4) লোড ব্যালেন্সার gRPC ট্রাফিক সুষমভাবে বণ্টন করতে পারে না এবং এর সমাধান কী?
7. Client-side Load Balancing কী এবং gRPC কীভাবে এটি বাস্তবায়ন করে?
8. gRPC Deadlines এবং Context Cancellation কীভাবে মাইক্রোসার্ভিসের ক্যাসকেডিং রিসোর্স অপচয় রোধ করে?
9. HTTP Trailers কী এবং gRPC কেন সাধারণ হেডারের বদলে ট্রেইলারে স্ট্যাটাস কোড (`grpc-status`) পাঠায়?
10. gRPC-Web কী এবং সাধারণ ব্রাউজার থেকে সরাসরি gRPC কল করতে কেন একটি প্রক্সি (যেমন Envoy) প্রয়োজন হয়?

### Advanced / Staff-Level Questions
11. কোটি কোটি রিকোয়েস্টের মাইক্রোসার্ভিস আর্কিটেকচারে Protobuf Backward ও Forward Compatibility কীভাবে পরিচালিত হয় (ফিল্ড ডিলিট বা টাইপ পরিবর্তনের নিয়ম)?
12. gRPC Interceptors কীভাবে কাজ করে এবং ডিস্ট্রিবিউটেড ট্রেসিং (OpenTelemetry TraceID injection), অথেনটিকেশন ও রেট লিমিটিংয়ে কীভাবে এটি ব্যবহার করবেন?
13. গ্লোবাল ডিস্ট্রিবিউটেড ক্লাস্টারে Envoy Service Mesh কীভাবে xDS প্রোটোকলের মাধ্যমে gRPC ক্লায়েন্টগুলোকে রিয়েল-টাইমে ডাইনামিক এন্ডপয়েন্ট লিস্ট আপডেট প্রদান করে?
14. gRPC Keepalive এবং Connection Ping/Pong সেটিংস কীভাবে ফায়ারওয়ালের সাইলেন্ট টিসিপি সকেট ড্রপ হওয়া প্রতিরোধ করে?
15. আল্ট্রা-লো ল্যাটেন্সি সিস্টেমে FlatBuffers বা Cap'n Proto কীভাবে Protobuf-এর চেয়েও দ্রুত (Zero-Copy Deserialization) পারফরম্যান্স দেয় এবং কেন গুগল তবুও gRPC-তে Protobuf বেছে নিয়েছে?
```

---

## 🔑 Answer Key & Self-Test Evaluation

<details>
<summary>👉 <b>Answer Key ও সমাধান দেখতে এখানে ক্লিক করুন</b></summary>

1. **সংজ্ঞা:** গুগল উদ্ভাবিত হাই-পারফরম্যান্স RPC ফ্রেমওয়ার্ক যা বাইনারি প্রোটোবাফ ও HTTP/2 ব্যবহার করে রিমোট মেথডকে লোকাল ফাংশনের মতো এক্সিকিউট করে।
2. **Protobuf:** গুগলের ল্যাঙ্গুয়েজ-অজ্ঞেয়বাদী বাইনারি সিরিয়ালাইজেশন মেকানিজম যা টেক্সটের বদলে কম্প্যাক্ট বাইনারি বিটে ডেটা এনকোড করে।
3. **ট্রান্সপোর্ট:** কঠোরভাবে HTTP/2 (এবং বর্তমানে পরীক্ষামূলক HTTP/3)।
4. **ট্যাগ নম্বর অপরিবর্তনীয়তা:** ওয়্যারে কোনো ফিল্ডের নাম যায় না, কেবল ট্যাগ নম্বর যায়। ট্যাগ পরিবর্তন করলে নতুন কোড পুরোনো ডেটা পার্স করতে পারবে না (ব্রেকিং চেঞ্জ)।
5. **৪টি মোড:** Unary, Server Streaming, Client Streaming, Bi-directional Streaming।
6. **L4 ব্যালেন্সার সমস্যা:** gRPC একটি লং-লিভড টিসিপি কানেকশন খুলে রাখে; L4 ব্যালেন্সার কেবল কানেকশন চেনে, ফলে সব রিকোয়েস্ট একটি পডেই চলে যায়। সমাধান: L7 বা Client-side LB।
7. **Client-side LB:** ক্লায়েন্ট নিজেই সার্ভিসের সমস্ত পড আইপি জেনে নিজের লোকাল কোডে রাউন্ড-রবিন বা লিস্ট-কানেকশন অ্যালগরিদম চালিয়ে সরাসরি বিভিন্ন পডে রিকোয়েস্ট পাঠায়।
8. **Deadlines & Cancellation:** ক্লায়েন্ট ডেডলাইন শেষ হলে কন্টেক্সট ক্যানসেল করে, gRPC সিগন্যাল ব্যাকএন্ডে চলে যায় এবং সার্ভার ডাটাবেজ কুয়েরি তৎক্ষণাৎ বন্ধ করে দেয়।
9. **HTTP Trailers:** বডির শেষে হেডার পাঠানো। স্ট্রিমিং শেষ হওয়ার পর আসল সাকসেস বা এরর স্ট্যাটাস নির্ভুলভাবে জানাতে এটি অপরিহার্য।
10. **gRPC-Web:** ব্রাউজারের জাভাস্ক্রিপ্ট সরাসরি HTTP/2 ফ্রেমিং বা ট্রেইলার্স এক্সেস করতে পারে না; Envoy প্রক্সি ব্রাউজারের রিকোয়েস্টকে আসল gRPC-তে রূপান্তর করে।
11. **Compatibility Rules:** ফিল্ড নম্বর কখনো পরিবর্তন করবেন না; নতুন ফিল্ড যোগ করলে অপশনাল রাখুন; কোনো ফিল্ড বাদ দিলে তার নম্বর `reserved` করে দিন।
12. **Interceptors:** মিডলওয়্যারের মতো। প্রতিটি RPC কলের আগে ও পরে লগিং, মেট্রিক্স এবং মেটাডাটা হেডার ইনজেকশন স্বয়ংক্রিয়ভাবে এক্সিকিউট করে।
13. **Envoy xDS:** ক্লায়েন্ট এনভয় কন্ট্রোল প্লেনের সাথে কানেক্ট থাকে এবং ব্যাকগ্রাউন্ডে নতুন পড ওঠা বা নামার সাথে সাথে ডাইনামিক এন্ডপয়েন্ট টেবিল আপডেট পায়।
14. **Keepalive Settings:** নির্দিষ্ট সময় পর পর টিসিপি লেভেলে পিং পাঠানো, যাতে ক্লাউড ফায়ারওয়াল বা লোড ব্যালেন্সার নিষ্ক্রিয় ভেবে সকেট ড্রপ না করে।
15. **FlatBuffers vs Protobuf:** FlatBuffers-এ কোনো ডিসিরিয়ালাইজেশন স্টেপই নেই (Zero-copy memory access); তবে Protobuf-এর কোড সাইজ ছোট, অধিক ফিচার সমৃদ্ধ এবং পরিপক্ক।

</details>
