# Module 02 · Networking

**Goal:** Understand the protocols and edge components behind service communication.  
**Suggested time:** 6–8 hours

## Learning outcomes

- [ ] Trace a request from a client to a service and back.
- [ ] Choose HTTP, WebSocket, Server-Sent Events (SSE), gRPC, or asynchronous messaging.
- [ ] Explain transport, security, authentication, and browser policy separately.

## Topic map

| Layer / concern | Topics | Key decision |
|---|---|---|
| Transport | TCP/IP, UDP, QUIC | Reliability, ordering, congestion, and handshake cost |
| Web protocols | HTTP/1.1, HTTP/2, HTTP/3, HTTPS | Connection reuse, multiplexing, head-of-line blocking |
| Naming and edge | DNS, content delivery network (CDN) | Resolution, caching, routing, and origin protection |
| Security | TLS/SSL | Identity, confidentiality, certificate lifecycle |
| Real time | WebSocket, SSE | Bidirectional vs server-to-client streams |
| APIs | REST, gRPC, GraphQL | Contract, clients, payload, streaming, and evolution |
| Identity/browser | OAuth 2.0, JWT, CORS, cookies, sessions | Delegation, credential storage, revocation, and origin rules |

## বাংলা deep-learning prompt

একবারে একটি topic বসিয়ে prompt-টি কপি করুন: `TCP/IP`, `UDP`, `HTTP/1.1`, `HTTP/2`, `HTTP/3`, `QUIC`, `DNS`, `CDN`, `SSL/TLS`, `HTTPS`, `WebSocket`, `Server-Sent Events (SSE)`, `gRPC`, `REST API`, `GraphQL`, `OAuth 2.0`, `JWT`, `CORS`, `Cookies`, `Session Management`।

```text
আমি Senior Software Engineer/Staff Engineer/System Design interview-এর জন্য [TOPIC] শিখছি। আমাকে topic-টি basic থেকে advanced পর্যন্ত পরিষ্কার বাংলায় গভীরভাবে শেখান। Technical term-এর English নাম পাশে রাখবেন।

উত্তরটি এই structure-এ দিন:
1. সহজ সংজ্ঞা ও intuitive mental model
2. কেন প্রয়োজন এবং কখন ব্যবহার করা হয়
3. এটি কীভাবে কাজ করে—step-by-step request/data flow
4. একটি সহজ example এবং একটি production/real-life example
5. protocol/components, implementation details ও গুরুত্বপূর্ণ variants
6. alternatives-এর সঙ্গে comparison table
7. trade-offs, limitations, latency impact, edge cases ও failure modes
8. scalability, reliability, security ও observability considerations
9. Senior/Staff-level interview-এ কীভাবে explain করব
10. common mistakes এবং likely follow-up questions
11. 5টি basic, 5টি intermediate ও 5টি advanced practice question আলাদা copyable code block-এ দিন
12. concise answer key শেষে আলাদা section-এ দিন, যাতে আগে self-test করতে পারি

Topic: [TOPIC]
Context: Networking and Service Communication
```

## Study path

1. Draw DNS lookup, connection setup, TLS handshake, HTTP request, and response.
2. Compare HTTP versions and identify which improvements depend on TCP or QUIC.
3. Compare WebSocket and SSE for chat, notifications, and live dashboards.
4. Design both browser and service-to-service authentication flows.
5. Separate OAuth authorization, OpenID Connect identity, JWT format, cookies, sessions, and CORS policy.

## Exercise

Design network communication for a collaborative dashboard: browsers load static assets globally, call APIs, and receive live updates while internal services use typed contracts.

## After-study checkpoint

1. Why can HTTP/2 still experience transport-level head-of-line blocking?
2. What does a CDN cache key need to include?
3. When is SSE simpler than WebSocket?
4. Why is JWT not a session-management strategy by itself?
5. Which security problem does CORS solve—and which does it not solve?

**Complete when:** you can draw the full request path and justify each protocol used in the dashboard.

---

# 📚 Deep Learning Study Guide: Networking (বাংলায়)

### Basic থেকে Advanced, Real-life Example ও Practice Questions সহ

```
Networking Roadmap
├── ১. Transport Layer Protocols (TCP/IP, UDP, QUIC)
├── ২. Web Application Protocols (HTTP/1.1, HTTP/2, HTTP/3, HTTPS)
├── ৩. Naming, Routing & Edge (DNS, CDN)
├── ৪. Network Security & Encryption (SSL/TLS, HTTPS)
├── ৫. Real-time Communication (WebSocket, Server-Sent Events / SSE)
├── ৬. API Styles & Architectural Contracts (REST API, gRPC, GraphQL)
└── ৭. Identity, Browser Security & Sessions (OAuth 2.0, JWT, CORS, Cookies, Session Management)
```

---

## ১. TCP/IP

### Basic
TCP/IP হলো ইন্টারনেটের ভিত্তি — এটা একটা প্রোটোকল সুইট যা ডেটা কীভাবে পাঠানো, রাউট করা এবং প্রাপকের কাছে পৌঁছানো নিশ্চিত করে। এতে ৪টা লেয়ার থাকে: Application, Transport, Internet, Network Access।

- **IP (Internet Protocol)**: প্রতিটা ডিভাইসকে একটা ইউনিক অ্যাড্রেস (IP address) দেয় এবং প্যাকেট রাউটিং করে।
- **TCP (Transmission Control Protocol)**: Connection-oriented, reliable ডেটা ডেলিভারি নিশ্চিত করে — 3-way handshake (SYN, SYN-ACK, ACK) এর মাধ্যমে কানেকশন তৈরি হয়।

### Advanced
- TCP-তে **flow control** (sliding window) এবং **congestion control** (slow start, congestion avoidance, fast retransmit) থাকে যা নেটওয়ার্ক ওভারলোড এড়ায়।
- প্রতিটা প্যাকেটের **sequence number** থাকে, তাই হারানো বা out-of-order প্যাকেট আবার সাজানো/পাঠানো যায় (retransmission)।
- **4-way handshake** দিয়ে কানেকশন বন্ধ হয় (FIN, ACK, FIN, ACK)।
- TCP vs UDP এর মূল পার্থক্য হলো reliability vs speed — TCP guarantee দেয় কিন্তু overhead বেশি।

### আরও Advanced (Expert-Level)
- **OSI vs TCP/IP model**: OSI 7-layer মডেল থিওরিটিক্যাল, বাস্তবে TCP/IP এর 4-layer (কখনো 5-layer বলা হয়: Physical, Data Link, Network, Transport, Application) মডেলই ব্যবহৃত হয়।
- **Nagle's Algorithm**: ছোট ছোট প্যাকেট একত্রে পাঠিয়ে নেটওয়ার্ক efficiency বাড়ায়, কিন্তু real-time অ্যাপ্লিকেশনে delay তৈরি করতে পারে (তখন `TCP_NODELAY` সেট করা হয়)।
- **TCP window scaling**: high-bandwidth, high-latency নেটওয়ার্কে (long fat networks) throughput বাড়াতে window size ৬৫৫৩৫ বাইটের সীমা ছাড়িয়ে scale করা যায়।
- **TIME_WAIT state**: কানেকশন ক্লোজ হওয়ার পরও একটা নির্দিষ্ট সময় (সাধারণত 2MSL) সকেট TIME_WAIT স্টেটে থাকে — high-traffic সার্ভারে port exhaustion সমস্যা তৈরি করতে পারে।
- **Head-of-line blocking at TCP layer**: এটাই মূল কারণ যে HTTP/2 এখনও TCP-তে সমস্যায় পড়ে, যা HTTP/3/QUIC সমাধান করে।
- **SYN flood attack**: DoS attack যেখানে attacker অনেক SYN পাঠায় কিন্তু ACK পাঠায় না, সার্ভারের half-open connection queue ভরে ফেলে — SYN cookies দিয়ে প্রতিরোধ করা হয়।

### Real-life Example
তুমি যখন কোনো ওয়েবসাইট ব্রাউজ করো বা ফাইল ডাউনলোড করো, তখন TCP নিশ্চিত করে প্রতিটা ডেটা প্যাকেট সঠিক অর্ডারে, কোনো ক্ষতি ছাড়া পৌঁছায় — যেমন কুরিয়ার সার্ভিস যেখানে প্রতিটা প্যাকেজের ট্র্যাকিং নাম্বার থাকে এবং ডেলিভারি কনফার্ম করা হয়।

### Practice Questions
```
1. TCP এবং UDP এর মধ্যে পার্থক্য কী? কখন কোনটা ব্যবহার করবে?
2. TCP 3-way handshake প্রসেসটা ব্যাখ্যা করো।
3. TCP কীভাবে reliable ডেটা ডেলিভারি নিশ্চিত করে?
4. Congestion control এবং flow control এর মধ্যে পার্থক্য কী?
5. TCP connection কীভাবে টার্মিনেট হয় (4-way handshake)?
```

---

## ২. UDP

### Basic
UDP (User Datagram Protocol) হলো connectionless, lightweight প্রোটোকল। এতে কোনো handshake নেই, reliability guarantee নেই, কিন্তু স্পিড বেশি কারণ overhead কম।

### Advanced
- UDP এ কোনো acknowledgment, retransmission বা ordering নেই — অ্যাপ্লিকেশন লেয়ারে এগুলো হ্যান্ডেল করতে হয় যদি দরকার হয়।
- Header size মাত্র ৮ বাইট (TCP এর তুলনায় অনেক কম, TCP এর header ২০ বাইট+)।
- **Use case selection**: রিয়েল-টাইম অ্যাপ্লিকেশনে সামান্য প্যাকেট লস acceptable কিন্তু delay নয় — তাই UDP প্রেফার করা হয়।

### আরও Advanced (Expert-Level)
- **Multicast/Broadcast support**: UDP এক সাথে একাধিক receiver-কে ডেটা পাঠাতে পারে (multicast), যা TCP এর connection-oriented প্রকৃতির কারণে সহজ নয় — video conferencing বা IPTV তে ব্যবহৃত হয়।
- **Application-layer reliability**: DNS, DHCP, TFTP, VoIP এর মতো প্রোটোকল UDP এর উপর নিজস্ব reliability layer বসায় (যেমন RTP + RTCP ভিডিও/অডিও স্ট্রিমিং এ)।
- **UDP-based protocols বিল্ডিং ব্লক**: QUIC/HTTP3 নিজেই UDP এর উপর নিজস্ব reliability, ordering ও encryption ইমপ্লিমেন্ট করে দেখায় যে UDP একটা flexible ফাউন্ডেশন হিসেবে কাজ করে।
- **Checksum**: UDP এ optional checksum থাকে (IPv4 তে), যা corrupted প্যাকেট detect করে কিন্তু recover করে না — শুধু drop করে দেয়।
- **UDP flood / amplification attack**: DNS বা NTP এর মতো সার্ভিসে ছোট রিকোয়েস্ট পাঠিয়ে বড় response ভিক্টিমের কাছে পাঠানো হয় (DDoS amplification) — spoofed source IP ব্যবহার করে।

### Real-life Example
ভিডিও কল (Zoom, Google Meet), অনলাইন গেমিং, বা লাইভ স্ট্রিমিং — এখানে একটা ফ্রেম মিস হলেও সমস্যা নেই, কিন্তু ডিলে হলে অভিজ্ঞতা খারাপ হয়ে যায়। তাই UDP ব্যবহার হয়।

### Practice Questions
```
1. UDP কেন TCP এর চেয়ে ফাস্ট?
2. কোন কোন real-world application UDP ব্যবহার করে এবং কেন?
3. UDP তে reliability না থাকলে ভিডিও স্ট্রিমিং কীভাবে smooth থাকে?
4. TCP এর বদলে UDP ব্যবহার করলে কী কী trade-off হয়?
```

---

## ৩. HTTP/1.1

### Basic
HTTP/1.1 হলো ওয়েব কমিউনিকেশনের স্ট্যান্ডার্ড প্রোটোকল যেখানে ক্লায়েন্ট (browser) সার্ভারে request পাঠায় এবং সার্ভার response দেয়। এটা **text-based** এবং **request-response** মডেলে কাজ করে।

### Advanced
- **Persistent connections**: HTTP/1.0 এর মতো প্রতি রিকোয়েস্টে নতুন TCP কানেকশন লাগে না — একই কানেকশনে একাধিক রিকোয়েস্ট যায় (`Connection: keep-alive`)।
- **Head-of-line (HOL) blocking**: একই কানেকশনে রিকোয়েস্টগুলো sequential ভাবে প্রসেস হয়, একটা স্লো রিকোয়েস্ট বাকিগুলোকে ব্লক করে দেয়।
- **Pipelining** সাপোর্ট করে কিন্তু বাস্তবে খুব একটা ব্যবহৃত হয় না কারণ ordering সমস্যা তৈরি করে।
- Browsers সাধারণত একই ডোমেইনে ৬টা পর্যন্ত parallel TCP connection খোলে HOL blocking এড়াতে।

### আরও Advanced (Expert-Level)
- **Chunked Transfer Encoding**: রেসপন্সের সাইজ আগে থেকে না জানলেও ডেটা ছোট ছোট chunk-এ পাঠানো যায় (`Transfer-Encoding: chunked`), যা streaming response এ কাজে লাগে।
- **Domain sharding**: HTTP/1.1 এর 6-connection সীমা এড়াতে ডেভেলপাররা আগে অ্যাসেট বিভিন্ন সাবডোমেইনে ভাগ করে দিতেন (যেমন `img1.site.com`, `img2.site.com`) — HTTP/2 এর পর এটা আর দরকার হয় না, বরং ক্ষতিকর।
- **Caching headers**: `ETag`, `Last-Modified`, `Cache-Control` দিয়ে ব্রাউজার কন্টেন্ট revalidate করে বা cache থেকে সরাসরি সার্ভ করে — নেটওয়ার্ক রাউন্ড-ট্রিপ বাঁচায়।
- **Content negotiation**: `Accept`, `Accept-Encoding`, `Accept-Language` হেডার দিয়ে ক্লায়েন্ট-সার্ভার একই রিসোর্সের বিভিন্ন ফরম্যাট/ভাষা নেগোশিয়েট করতে পারে।

### Real-life Example
তুমি যখন একটা ই-কমার্স সাইট লোড করো, ব্রাউজার একই সার্ভারে একাধিক connection খুলে CSS, JS, ইমেজ ফাইল আলাদাভাবে fetch করে — যেন এক দোকানে অনেক কাউন্টার আছে, কিন্তু প্রতিটা কাউন্টারে এক এক সময় একজনই সার্ভিস পায়।

### Practice Questions
```
1. HTTP/1.1 এ Head-of-line blocking কী এবং এটা কীভাবে পারফরম্যান্স প্রভাবিত করে?
2. Persistent connection (keep-alive) কীভাবে কাজ করে?
3. HTTP/1.0 এবং HTTP/1.1 এর মূল পার্থক্য কী?
4. কেন ব্রাউজার একই ডোমেইনে একাধিক TCP connection খোলে?
```

---

## ৪. HTTP/2

### Basic
HTTP/2 হলো HTTP/1.1 এর পারফরম্যান্স ইমপ্রুভড ভার্সন — এটা **binary protocol** এবং একটা সিঙ্গেল TCP কানেকশনে multiple request/response parallel ভাবে পাঠাতে পারে।

### Advanced
- **Multiplexing**: একটা কানেকশনে একাধিক স্ট্রিম (request/response) parallel ভাবে চলে, তাই application-layer HOL blocking সমাধান হয় (কিন্তু TCP-layer HOL blocking থেকেই যায়)।
- **Header Compression (HPACK)**: রিপিটেড হেডার ডেটা কমপ্রেস করে ব্যান্ডউইথ বাঁচায়।
- **Server Push**: সার্ভার ক্লায়েন্টের রিকোয়েস্ট ছাড়াই প্রয়োজনীয় রিসোর্স আগে থেকেই পাঠাতে পারে (যদিও এখন কম ব্যবহৃত)।
- **Stream Prioritization**: গুরুত্বপূর্ণ রিসোর্স আগে লোড করার জন্য প্রায়োরিটি সেট করা যায়।

### আরও Advanced (Expert-Level)
- **Flow control per-stream**: HTTP/2 প্রতিটা স্ট্রিমের জন্য আলাদা flow control window রাখে, একটা কানেকশন-লেভেল window এর পাশাপাশি — যাতে একটা স্ট্রিম পুরো কানেকশন দখল করে না নেয়।
- **Binary framing layer**: টেক্সট-বেসড HTTP/1.1 এর বদলে HTTP/2 ডেটাকে ছোট ছোট binary frame এ ভাগ করে পাঠায় (HEADERS frame, DATA frame, ইত্যাদি), যা পার্সিং দ্রুত ও কম error-prone করে।
- **Stream dependency tree**: prioritization শুধু একটা weight নয়, বরং stream গুলোর মধ্যে dependency tree তৈরি করা যায় (কোন স্ট্রিম কার উপর নির্ভরশীল)।
- **TLS প্রায় mandatory**: বেশিরভাগ ব্রাউজার শুধু HTTPS এর উপর HTTP/2 সাপোর্ট করে (যদিও স্পেসিফিকেশনে plaintext সাপোর্ট আছে)।

### Real-life Example
একটা বড় নিউজ ওয়েবসাইট যেখানে অনেক ইমেজ, স্ক্রিপ্ট, স্টাইলশিট লোড হয় — HTTP/2 এ এই সব একইসাথে একটা কানেকশনে পাঠানো যায়, ফলে পেজ লোড টাইম কমে যায়, যেমন একটা মাল্টি-লেন হাইওয়ে যেখানে একসাথে অনেক গাড়ি চলতে পারে।

### Practice Questions
```
1. HTTP/2 এ multiplexing কীভাবে কাজ করে এবং এটা কী সমস্যা সমাধান করে?
2. HPACK header compression কী এবং কেন দরকার?
3. HTTP/2 এখনও TCP-layer HOL blocking সমস্যায় পড়ে কেন?
4. Server Push কী এবং এটা কেন এখন কম ব্যবহৃত হয়?
```

---

## ৫. HTTP/3

### Basic
HTTP/3 হলো HTTP এর সর্বশেষ ভার্সন যা TCP এর বদলে **QUIC** প্রোটোকল (UDP-based) ব্যবহার করে, যা speed এবং connection reliability আরও বাড়ায়।

### Advanced
- TCP-layer HOL blocking সম্পূর্ণভাবে দূর হয় কারণ QUIC এর প্রতিটা স্ট্রিম independent — একটা স্ট্রিমে প্যাকেট লস হলে বাকি স্ট্রিম প্রভাবিত হয় না।
- **0-RTT / 1-RTT connection establishment**: বারবার কানেকশন করলে হ্যান্ডশেক টাইম কমে যায়, যা mobile নেটওয়ার্কে বিশেষভাবে উপকারী।
- **Connection migration**: ইউজার WiFi থেকে মোবাইল ডেটায় সুইচ করলেও কানেকশন ব্রেক হয় না (connection ID দিয়ে ট্র্যাক হয়, IP দিয়ে নয়)।
- Built-in TLS 1.3 encryption — সিকিউরিটি আরও শক্তিশালী এবং integrated।

### আরও Advanced (Expert-Level)
- **UDP-based deployment challenges**: অনেক corporate firewall/NAT UDP ট্রাফিক ব্লক বা throttle করে, তাই HTTP/3 প্রায়ই TCP/HTTP2 তে fallback করার লজিক রাখতে হয়।
- **Amplification attack mitigation**: QUIC হ্যান্ডশেকে address validation ব্যবহার করা হয় যাতে UDP-based amplification attack প্রতিরোধ করা যায়।
- **Kernel-space vs user-space**: TCP সাধারণত OS kernel এ ইমপ্লিমেন্টেড, কিন্তু QUIC/HTTP3 user-space এ চলে — এটা দ্রুত আপডেট/ইনোভেশন সম্ভব করে কিন্তু CPU overhead বেশি হতে পারে।
- **Adoption**: বড় বড় CDN এবং cloud provider (Cloudflare, Google, Fastly) HTTP/3 সাপোর্ট করে, তবে এখনো TCP/HTTP2 ব্যাকআপ হিসেবে থাকে বিস্তৃত compatibility এর জন্য।

### Real-life Example
Google, YouTube, Facebook এখন HTTP/3 ব্যবহার করে — তুমি মেট্রোতে যাচ্ছ, WiFi থেকে মোবাইল ডেটায় সুইচ হচ্ছে, কিন্তু ভিডিও বাফার না করেই চলতে থাকে কারণ QUIC connection migration সাপোর্ট করে।

### Practice Questions
```
1. HTTP/3 কেন TCP এর বদলে QUIC (UDP-based) ব্যবহার করে?
2. Connection migration কী এবং এটা কীভাবে কাজ করে?
3. HTTP/2 এর তুলনায় HTTP/3 কীভাবে HOL blocking সমস্যা সমাধান করে?
4. 0-RTT handshake এর সুবিধা কী?
```

---

## ৬. QUIC

### Basic
QUIC (Quick UDP Internet Connections) হলো Google-তৈরি একটা transport-layer প্রোটোকল যা UDP এর উপর ভিত্তি করে তৈরি, কিন্তু TCP এর reliability এবং TLS এর security একসাথে দেয়।

### Advanced
- QUIC নিজস্বভাবে **reliability, ordering, congestion control** ইমপ্লিমেন্ট করে — UDP এর উপরে কিন্তু TCP এর মতো গ্যারান্টি সহ।
- **Independent streams**: multiplexed streams একে অপরের থেকে সম্পূর্ণ আলাদা, তাই এক স্ট্রিমের প্যাকেট লস অন্য স্ট্রিমকে ব্লক করে না।
- Encryption (TLS 1.3) built-in — handshake এবং encryption একসাথে হয়, তাই সময় বাঁচে।
- HTTP/3 এর ফাউন্ডেশন প্রোটোকল হলো QUIC।

### আরও Advanced (Expert-Level)
- **Connection ID**: QUIC কানেকশনকে IP:port এর বদলে একটা Connection ID দিয়ে ট্র্যাক করে, যার ফলে NAT rebinding বা network change হলেও কানেকশন টিকে থাকে (connection migration এর মূল কারণ)।
- **Loss detection ও congestion control**: TCP এর মতোই কিন্তু QUIC এ এটা আরও flexible ও pluggable — Google এর BBR এর মতো আধুনিক congestion control algorithm সহজে ইন্টিগ্রেট করা যায়।
- **QUIC এর security by design**: হ্যান্ডশেকের বেশিরভাগ অংশ encrypted, এমনকি TCP এর মতো header ও metadata কিছুটা লুকানো থাকে, যা middlebox ossification (নেটওয়ার্ক ডিভাইস প্রোটোকল পরিবর্তনে বাধা দেওয়া) প্রতিরোধ করে।
- **HTTP/3 ছাড়াও ব্যবহার**: QUIC শুধু ওয়েবের জন্য নয় — DNS-over-QUIC, বা অন্য কাস্টম প্রোটোকলেও ব্যবহার করা যায়।

### Real-life Example
মোবাইল ইন্টারনেটে দুর্বল নেটওয়ার্কে থাকা অবস্থায় ওয়েবসাইট ব্রাউজ করার সময় QUIC দ্রুত রিকভার করে প্যাকেট লস থেকে — অনেকটা এমন যেন একাধিক আলাদা কুরিয়ার লেন থাকা, যেখানে একটা লেনে জ্যাম হলেও বাকি লেনগুলো চলতে থাকে।

### Practice Questions
```
1. QUIC কেন UDP এর উপর তৈরি হয়েছে, TCP এর উপর নয়?
2. QUIC কীভাবে reliability নিশ্চিত করে যেখানে UDP তা করে না?
3. QUIC এবং TCP+TLS এর মধ্যে পার্থক্য কী?
```

---

## ৭. DNS

### Basic
DNS (Domain Name System) হলো ইন্টারনেটের "phonebook" — এটা মানুষ-পঠনযোগ্য ডোমেইন নাম (যেমন google.com) কে IP address এ রূপান্তর করে।

### Advanced
DNS resolution প্রসেস কয়েকটা ধাপে হয়:
1. **Browser cache** চেক করা হয়
2. **OS cache** চেক করা হয়
3. **Recursive DNS resolver** (সাধারণত ISP প্রোভাইড করে) কোয়েরি করে
4. **Root nameserver** → **TLD nameserver** (.com, .org) → **Authoritative nameserver** এই চেইনে গিয়ে IP পাওয়া যায়

- **DNS record types**: A (IPv4), AAAA (IPv6), CNAME (alias), MX (mail), TXT, NS।
- **TTL (Time To Live)**: কতক্ষণ একটা রেকর্ড cache রাখা হবে সেটা নিয়ন্ত্রণ করে।
- **DNS caching** বিভিন্ন লেভেলে হয় (browser, OS, resolver) — পারফরম্যান্স বাড়ায়।

### আরও Advanced (Expert-Level)
- **DNS over HTTPS (DoH) / DNS over TLS (DoT)**: DNS কোয়েরি সাধারণত plaintext-এ যায় (privacy risk), DoH/DoT এনক্রিপ্ট করে পাঠায় — ব্রাউজার-level privacy বাড়ায়।
- **DNSSEC**: DNS response এর authenticity ভেরিফাই করার জন্য cryptographic signature ব্যবহার করে, DNS spoofing/cache poisoning প্রতিরোধ করে।
- **DNS load balancing / GeoDNS**: একই ডোমেইনের জন্য ইউজারের লোকেশন অনুযায়ী ভিন্ন IP রিটার্ন করা হয় — closest server-এ রাউট করার জন্য, CDN এর সাথে মিলে কাজ করে।
- **Anycast routing**: root DNS server গুলো anycast ব্যবহার করে — একই IP address বিশ্বের একাধিক লোকেশন থেকে serve করা হয়, ইউজার নিকটতমটাতে পৌঁছায়।
- **Negative caching**: DNS "NXDOMAIN" (domain not found) response ও একটা নির্দিষ্ট সময় cache করে রাখা হয়, বারবার একই ফেইলড কোয়েরি এড়াতে।

### Real-life Example
তুমি ব্রাউজারে `facebook.com` টাইপ করলে DNS সেটাকে `157.240.x.x` এর মতো একটা IP address এ কনভার্ট করে দেয় — অনেকটা ফোনবুকে নাম দিয়ে নাম্বার খোঁজার মতো, কারণ তুমি নাম্বার মুখস্থ রাখতে চাও না।

### Practice Questions
```
1. DNS resolution এর পুরো প্রসেস (step by step) ব্যাখ্যা করো।
2. Recursive এবং Authoritative DNS server এর মধ্যে পার্থক্য কী?
3. TTL কী এবং এটা DNS caching এ কীভাবে ভূমিকা রাখে?
4. A record এবং CNAME record এর পার্থক্য কী?
5. DNS caching কেন গুরুত্বপূর্ণ পারফরম্যান্সের জন্য?
```

---

## ৮. CDN

### Basic
CDN (Content Delivery Network) হলো বিশ্বব্যাপী ছড়ানো সার্ভারের একটা নেটওয়ার্ক যা কন্টেন্ট (ইমেজ, ভিডিও, স্ট্যাটিক ফাইল) ইউজারের কাছাকাছি লোকেশন থেকে ডেলিভার করে, যাতে লেটেন্সি কমে।

### Advanced
- **Edge servers**: বিভিন্ন জিওগ্রাফিক লোকেশনে থাকা সার্ভার যেখানে কন্টেন্ট ক্যাশ করা থাকে।
- **Origin server**: মূল সার্ভার যেখান থেকে CDN প্রথমবার কন্টেন্ট fetch করে (cache miss হলে)।
- **Cache invalidation/purging**: কন্টেন্ট আপডেট হলে পুরনো cache কীভাবে রিফ্রেশ করা হয়।
- CDN শুধু static content নয়, **dynamic content acceleration** ও করতে পারে (routing optimization দিয়ে)।
- **DDoS protection** এবং load balancing এর কাজও CDN করে (Cloudflare, Akamai)।

### আরও Advanced (Expert-Level)
- **Cache-Control strategies**: `max-age`, `s-maxage`, `stale-while-revalidate` এর মতো ডিরেক্টিভ দিয়ে কতক্ষণ এজ সার্ভার কন্টেন্ট ধরে রাখবে এবং কীভাবে রিফ্রেশ করবে তা নিয়ন্ত্রণ করা যায়।
- **Edge computing**: আধুনিক CDN (Cloudflare Workers, AWS Lambda@Edge) এজ সার্ভারে কোড execute করার সুযোগ দেয় — ইউজারের কাছাকাছি লজিক প্রসেস করে লেটেন্সি আরও কমায়।
- **Anycast এর ভূমিকা**: CDN নেটওয়ার্কেও anycast IP ব্যবহার করা হয় যাতে ইউজারের রিকোয়েস্ট স্বয়ংক্রিয়ভাবে নিকটতম এজ সার্ভারে রাউট হয়।
- **Cache key customization**: query parameter, header, বা cookie অনুযায়ী কীভাবে ভিন্ন ভিন্ন cache variant তৈরি হবে তা কাস্টমাইজ করা যায় (personalized content caching)।

### Real-life Example
Netflix বা YouTube ভিডিও যখন তুমি দেখো, সেটা আমেরিকার কোনো ডেটা সেন্টার থেকে না এসে তোমার কাছাকাছি (হয়তো ঢাকার) একটা edge সার্ভার থেকে আসে — তাই বাফারিং কম হয়।

### Practice Questions
```
1. CDN কীভাবে ওয়েবসাইটের লোড টাইম কমায়?
2. Origin server এবং Edge server এর মধ্যে পার্থক্য কী?
3. Cache invalidation কী এবং এটা কেন চ্যালেঞ্জিং?
4. CDN কীভাবে DDoS আক্রমণ থেকে সুরক্ষা দেয়?
```

---

## ৯. SSL/TLS

### Basic
SSL/TLS হলো এনক্রিপশন প্রোটোকল যা ক্লায়েন্ট এবং সার্ভারের মধ্যে ডেটা ট্রান্সমিশন সুরক্ষিত করে। TLS হলো SSL এর আধুনিক ও নিরাপদ ভার্সন (SSL এখন deprecated)।

### Advanced
- **TLS Handshake process**:
  1. Client Hello (supported cipher suites পাঠায়)
  2. Server Hello (সিলেক্টেড cipher + সার্টিফিকেট পাঠায়)
  3. Certificate verification (CA দিয়ে)
  4. Key exchange (asymmetric encryption দিয়ে shared secret তৈরি)
  5. এরপর symmetric encryption দিয়ে actual ডেটা ট্রান্সফার হয় (ফাস্ট হওয়ার জন্য)
- **Asymmetric vs Symmetric encryption**: হ্যান্ডশেকে asymmetric (public/private key), ডেটা ট্রান্সফারে symmetric ব্যবহার হয়।
- **TLS 1.3** এ handshake আরও দ্রুত (1-RTT, এমনকি 0-RTT resumption সম্ভব) এবং পুরনো insecure cipher বাদ দেওয়া হয়েছে।
- **Certificate Authority (CA)** সার্টিফিকেট ভেরিফাই করে ট্রাস্ট চেইন নিশ্চিত করে।

### আরও Advanced (Expert-Level)
- **Perfect Forward Secrecy (PFS)**: প্রতিটা সেশনের জন্য নতুন ephemeral key ব্যবহার করা হয় (যেমন Diffie-Hellman Ephemeral), যাতে ভবিষ্যতে private key leak হলেও পুরনো সেশনের ডেটা ডিক্রিপ্ট করা না যায়।
- **Certificate chain of trust**: Root CA → Intermediate CA → Leaf (server) certificate — ব্রাউজার এই পুরো চেইন ভেরিফাই করে, Root CA গুলো OS/browser এ pre-installed থাকে।
- **Mutual TLS (mTLS)**: শুধু সার্ভার নয়, ক্লায়েন্টও একটা সার্টিফিকেট দিয়ে নিজেকে প্রমাণ করে — মূলত microservice-to-microservice বা high-security API তে ব্যবহৃত হয়।
- **SNI (Server Name Indication)**: TLS handshake এর শুরুতে ক্লায়েন্ট কোন hostname চাইছে তা পাঠায়, যাতে একই IP তে multiple SSL certificate সহ multiple ডোমেইন হোস্ট করা যায়।
- **OCSP / Certificate Revocation**: কোনো সার্টিফিকেট compromise হলে সেটা revoke করার মেকানিজম (CRL, OCSP) থাকে যাতে ব্রাউজার সেটা আর trust না করে।

### Real-life Example
ব্যাংকিং ওয়েবসাইটে যখন তুমি লগইন করো, ব্রাউজারের URL বারে "🔒 https://" দেখায় — এটা TLS এর কারণে, যা তোমার পাসওয়ার্ড এবং ডেটা middle-man attack থেকে সুরক্ষিত রাখে।

### Practice Questions
```
1. TLS handshake প্রসেস স্টেপ বাই স্টেপ ব্যাখ্যা করো।
2. Symmetric এবং Asymmetric encryption এর মধ্যে পার্থক্য কী এবং TLS এ কোথায় কোনটা ব্যবহার হয়?
3. Certificate Authority (CA) এর ভূমিকা কী?
4. TLS 1.2 এবং TLS 1.3 এর মধ্যে মূল পার্থক্য কী?
5. Man-in-the-middle attack কীভাবে TLS প্রতিরোধ করে?
```

---

## ১০. HTTPS

### Basic
HTTPS হলো HTTP + TLS/SSL — অর্থাৎ HTTP প্রোটোকলের উপর এনক্রিপশন লেয়ার যোগ করা, যা ডেটা এনক্রিপ্ট করে পাঠায় এবং সার্ভারের identity ভেরিফাই করে।

### Advanced
- HTTPS তিনটা জিনিস নিশ্চিত করে: **Confidentiality** (এনক্রিপশন), **Integrity** (ডেটা পরিবর্তন হয়নি তার নিশ্চয়তা), **Authentication** (সার্ভার আসল কিনা)।
- Default port: HTTP এ 80, HTTPS এ 443।
- SEO এবং browser trust এ HTTPS এর প্রভাব আছে (Chrome এ HTTP সাইট "Not Secure" দেখায়)।
- **HSTS (HTTP Strict Transport Security)** হেডার ব্যবহার করে সার্ভার ব্রাউজারকে বলে দেয় সবসময় HTTPS ব্যবহার করতে।

### আরও Advanced (Expert-Level)
- **HSTS Preload list**: ব্রাউজার ভেন্ডররা একটা hardcoded list রাখে যেসব ডোমেইন সবসময় HTTPS-only, তাই প্রথম ভিজিটেও HTTP downgrade attack সম্ভব হয় না।
- **Mixed content issue**: HTTPS পেজে HTTP রিসোর্স (ইমেজ, স্ক্রিপ্ট) লোড করলে ব্রাউজার ব্লক করে বা ওয়ার্নিং দেয় — পুরো পেজের সিকিউরিটি গ্যারান্টি ভেঙে যেতে পারে বলে।
- **TLS termination point**: বড় সিস্টেমে HTTPS প্রায়ই লোড ব্যালেন্সার/CDN এ terminate হয় (এনক্রিপশন সেখানেই decrypt হয়), এরপর ইন্টার্নাল নেটওয়ার্কে plain HTTP ব্যবহার হতে পারে — এটা design trade-off।
- **Certificate pinning**: মোবাইল অ্যাপে নির্দিষ্ট certificate/public key hardcode করে রাখা হয়, যাতে compromised CA দিয়েও man-in-the-middle attack সম্ভব না হয়।

### Real-life Example
ই-কমার্স সাইটে ক্রেডিট কার্ড ইনফরমেশন দেওয়ার সময় HTTPS নিশ্চিত করে সেই ডেটা কেউ মাঝপথে চুরি করতে পারবে না।

### Practice Questions
```
1. HTTP এবং HTTPS এর পার্থক্য কী?
2. HTTPS কীভাবে ডেটা ইন্টিগ্রিটি নিশ্চিত করে?
3. HSTS কী এবং এটা কী সমস্যা সমাধান করে?
```

---

## ১১. WebSocket

### Basic
WebSocket হলো একটা প্রোটোকল যা ক্লায়েন্ট-সার্ভারের মধ্যে **full-duplex, persistent** কানেকশন তৈরি করে — মানে দুই দিক থেকেই যেকোনো সময় ডেটা পাঠানো যায়, বারবার রিকোয়েস্ট করার দরকার নেই।

### Advanced
- HTTP দিয়ে কানেকশন শুরু হয়ে **Upgrade header** এর মাধ্যমে WebSocket প্রোটোকলে switch হয় (HTTP → ws:// বা wss://)।
- একবার কানেকশন প্রতিষ্ঠিত হলে, এটা TCP এর উপর একটা persistent কানেকশন থেকে যায় — HTTP এর মতো request-response প্রতিবার লাগে না।
- Low latency, real-time bidirectional communication এর জন্য ideal।
- Scaling চ্যালেঞ্জ থাকে কারণ প্রতিটা কানেকশন সার্ভারে state রাখতে হয় (stateful), যা load balancing কে জটিল করে তোলে।

### আরও Advanced (Expert-Level)
- **Heartbeat/Ping-Pong frames**: কানেকশন জীবিত আছে কিনা যাচাই করতে এবং idle কানেকশন প্রক্সি/লোড ব্যালেন্সার দ্বারা টাইমআউট হওয়া এড়াতে নিয়মিত ping-pong frame পাঠানো হয়।
- **Horizontal scaling সমাধান**: multiple WebSocket সার্ভারের মধ্যে মেসেজ broadcast করতে Redis Pub/Sub বা message broker (Kafka, RabbitMQ) ব্যবহার করা হয়, যাতে ভিন্ন সার্ভারে কানেক্টেড ক্লায়েন্টরাও মেসেজ পায়।
- **Backpressure handling**: স্লো ক্লায়েন্ট বা ধীর নেটওয়ার্কে বেশি ডেটা পাঠালে সার্ভার মেমরি বাফার ওভারফ্লো হতে পারে — buffer limit এবং rate limiting দরকার হয়।
- **Fallback mechanism**: পুরনো ব্রাউজার বা কড়া ফায়ারওয়ালে WebSocket ব্লক হলে long-polling এ fallback করার লজিক (Socket.IO এর মতো লাইব্রেরি এটা অটোমেটিক হ্যান্ডেল করে)।

### Real-life Example
চ্যাট অ্যাপ্লিকেশন (যেমন WhatsApp Web) বা লাইভ স্টক প্রাইস আপডেট — সার্ভার নতুন মেসেজ আসামাত্র ক্লায়েন্টকে push করতে পারে, ক্লায়েন্টকে বারবার "নতুন মেসেজ আছে কিনা" জিজ্ঞাসা করতে হয় না।

### Practice Questions
```
1. WebSocket এবং HTTP এর মধ্যে মূল পার্থক্য কী?
2. WebSocket কানেকশন কীভাবে প্রতিষ্ঠিত হয় (handshake process)?
3. WebSocket কখন ব্যবহার করবে, আর কখন REST API যথেষ্ট?
4. WebSocket scaling এ কী কী challenge থাকে?
```

---

## ১২. SSE (Server-Sent Events)

### Basic
SSE হলো একটা টেকনোলজি যেখানে সার্ভার একটা single HTTP কানেকশনের মাধ্যমে ক্লায়েন্টকে ক্রমাগত (continuous) ডেটা push করতে পারে — কিন্তু এটা **one-directional** (শুধু সার্ভার → ক্লায়েন্ট)।

### Advanced
- সাধারণ HTTP কানেকশন ব্যবহার করে (`Content-Type: text/event-stream`), তাই WebSocket এর মতো আলাদা প্রোটোকল upgrade লাগে না।
- Built-in **auto-reconnect** সাপোর্ট আছে ব্রাউজারের `EventSource` API তে।
- WebSocket এর চেয়ে সহজ ইমপ্লিমেন্ট করা, কিন্তু bidirectional কমিউনিকেশন দরকার হলে এটা কাজ করবে না।
- HTTP/2 এর সাথে ভালো কাজ করে কারণ multiplexing এর কারণে অনেক SSE কানেকশন efficient ভাবে চলতে পারে।

### আরও Advanced (Expert-Level)
- **Event ID এবং resumption**: প্রতিটা event এ একটা `id` দেওয়া যায়, ব্রাউজার ডিসকানেক্ট হলে reconnect করার সময় `Last-Event-ID` হেডার পাঠায়, যাতে সার্ভার মিসড event গুলো আবার পাঠাতে পারে।
- **HTTP/1.1 এ কানেকশন সীমা সমস্যা**: HTTP/1.1 এ একটা ডোমেইনে ৬টা কানেকশনের সীমার কারণে অনেক ট্যাবে SSE ব্যবহার করলে সমস্যা হতে পারে — HTTP/2 এই সমস্যা দূর করে।
- **Load balancer/proxy compatibility**: কিছু প্রক্সি/লোড ব্যালেন্সার দীর্ঘ persistent HTTP কানেকশন buffer করে ফেলে, যার ফলে real-time delivery ব্যাহত হতে পারে — এজন্য `X-Accel-Buffering: no` এর মতো হেডার ব্যবহার করতে হয়।

### Real-life Example
লাইভ স্পোর্টস স্কোর আপডেট, নোটিফিকেশন ফিড, বা স্টক টিকার — যেখানে শুধু সার্ভার থেকে ক্লায়েন্টে ডেটা যাচ্ছে, ক্লায়েন্টের কিছু পাঠানোর দরকার নেই।

### Practice Questions
```
1. SSE এবং WebSocket এর মধ্যে পার্থক্য কী?
2. SSE কখন ব্যবহার করা উচিত?
3. SSE তে reconnection কীভাবে হ্যান্ডেল হয়?
```

---

## ১৩. gRPC

### Basic
gRPC হলো Google-তৈরি একটা high-performance RPC (Remote Procedure Call) ফ্রেমওয়ার্ক যা HTTP/2 এর উপর চলে এবং **Protocol Buffers (protobuf)** ব্যবহার করে ডেটা সিরিয়ালাইজ করে।

### Advanced
- **Protocol Buffers**: JSON এর চেয়ে অনেক compact এবং fast binary serialization format।
- **4 types of communication**: Unary (একটা request-একটা response), Server streaming, Client streaming, Bidirectional streaming।
- HTTP/2 এর multiplexing ব্যবহার করে একই কানেকশনে অনেকগুলো call parallel ভাবে করা যায়।
- **Strongly typed contracts** (.proto ফাইলের মাধ্যমে) — client এবং server এর মধ্যে interface strictly ডিফাইনড থাকে, code generation করা যায় বিভিন্ন ভাষায়।
- Microservices architecture তে ইন্টার্নাল সার্ভিস কমিউনিকেশনের জন্য খুব জনপ্রিয়।

### আরও Advanced (Expert-Level)
- **Deadline/Timeout propagation**: gRPC কলে deadline সেট করা যায় যা কল চেইনের মধ্য দিয়ে propagate হয় — একটা downstream সার্ভিস স্লো হলে পুরো চেইন দ্রুত fail করে (cascading failure এড়ায়)।
- **Interceptors (Middleware)**: authentication, logging, retry logic centralized ভাবে ইমপ্লিমেন্ট করার জন্য gRPC তে client/server interceptor ব্যবহার হয়।
- **Load balancing challenges**: gRPC persistent HTTP/2 কানেকশন ব্যবহার করে বলে traditional L4 (TCP-level) লোড ব্যালেন্সার effective ভাবে কানেকশনের ভেতরের রিকোয়েস্ট বিতরণ করতে পারে না — client-side load balancing বা L7 প্রক্সি (Envoy) দরকার হয়।
- **Browser limitation**: ব্রাউজার সরাসরি raw gRPC কল করতে পারে না (HTTP/2 trailer এর লিমিটেশনের কারণে) — এজন্য **gRPC-Web** ব্যবহার করতে হয়, যা একটা প্রক্সির মাধ্যমে ট্রান্সলেট হয়।

### Real-life Example
Netflix বা Uber এর মতো কোম্পানিতে যেখানে শত শত microservice একে অপরের সাথে কথা বলে, সেখানে gRPC ব্যবহার করা হয় কারণ REST/JSON এর চেয়ে এটা অনেক ফাস্ট এবং efficient — অনেকটা কোম্পানির অভ্যন্তরীণ ইন্টারকম সিস্টেমের মতো, যেখানে পাবলিক ফোন কলের (REST) চেয়ে দ্রুত ও structured কমিউনিকেশন হয়।

### Practice Questions
```
1. gRPC এবং REST API এর মধ্যে পার্থক্য কী?
2. Protocol Buffers কী এবং এটা কেন JSON এর চেয়ে ফাস্ট?
3. gRPC এর ৪ ধরনের streaming মডেল ব্যাখ্যা করো।
4. কখন gRPC ব্যবহার করবে, কখন REST ব্যবহার করবে?
```

---

## ১৪. REST API

### Basic
REST (Representational State Transfer) হলো একটা আর্কিটেকচারাল স্টাইল যেখানে HTTP মেথড (GET, POST, PUT, DELETE) ব্যবহার করে resource-ভিত্তিক অপারেশন করা হয়।

### Advanced
REST এর মূল principles:
- **Statelessness**: প্রতিটা request স্বয়ংসম্পূর্ণ, সার্ভার কোনো client state মনে রাখে না।
- **Resource-based**: সব কিছু একটা resource (URI দিয়ে identify করা হয়) — যেমন `/users/123`।
- **Uniform interface**: HTTP মেথড দিয়ে standard অপারেশন — GET (read), POST (create), PUT (update/replace), PATCH (partial update), DELETE।
- **Cacheable**: response cache করা যায় কিনা তা নির্দিষ্ট করা যায় (Cache-Control header)।
- **HATEOAS** (Hypermedia as the Engine of Application State) — response এ পরবর্তী সম্ভাব্য action এর লিংক থাকে (কম ব্যবহৃত হলেও pure REST এর অংশ)।
- **Idempotency**: GET, PUT, DELETE idempotent (বারবার করলেও একই result), কিন্তু POST নয়।

### আরও Advanced (Expert-Level)
- **API Versioning strategies**: URI versioning (`/v1/users`), header versioning (`Accept: application/vnd.api.v1+json`), বা query parameter versioning — প্রতিটার নিজস্ব trade-off আছে।
- **Pagination patterns**: Offset-based (`?page=2&limit=20`) সহজ কিন্তু বড় ডেটাসেটে স্লো; Cursor-based pagination বেশি efficient এবং consistent, বিশেষত রিয়েল-টাইম changing data তে।
- **Rate limiting**: `429 Too Many Requests` স্ট্যাটাস কোড এবং `X-RateLimit-*` হেডার দিয়ে API abuse প্রতিরোধ করা হয় (token bucket, sliding window algorithm দিয়ে ইমপ্লিমেন্টেড)।
- **HATEOAS বাস্তবতা**: বেশিরভাগ প্রোডাকশন API pure REST/HATEOAS মেনে চলে না — সেগুলোকে বলা হয় "RESTful" বা pragmatic REST।
- **Richardson Maturity Model**: REST API maturity মাপার একটা ফ্রেমওয়ার্ক (Level 0: single endpoint, Level 1: resources, Level 2: HTTP verbs, Level 3: HATEOAS)।

### Real-life Example
একটা ই-কমার্স অ্যাপ যেখানে `GET /products` দিয়ে প্রোডাক্ট লিস্ট পাওয়া যায়, `POST /orders` দিয়ে নতুন অর্ডার তৈরি হয়, `PUT /users/123` দিয়ে ইউজার প্রোফাইল আপডেট হয়।

### Practice Questions
```
1. REST API এর মূল প্রিন্সিপলগুলো কী কী?
2. PUT এবং PATCH এর মধ্যে পার্থক্য কী?
3. Idempotency কী? কোন HTTP মেথডগুলো idempotent?
4. Statelessness কেন REST API এর জন্য গুরুত্বপূর্ণ?
5. REST API তে ভালো ভাবে error handling কীভাবে করবে (status code সহ ব্যাখ্যা করো)?
```

---

## ১৫. GraphQL

### Basic
GraphQL হলো একটা query language এবং runtime যেখানে ক্লায়েন্ট ঠিক করে দেয় তার কী কী ডেটা লাগবে — একটা single endpoint দিয়ে, over-fetching বা under-fetching এড়িয়ে।

### Advanced
- **Single endpoint**: REST এ multiple endpoint লাগে (`/users`, `/posts`), GraphQL এ একটাই endpoint (`/graphql`), query দিয়ে ডেটা শেপ নির্ধারণ হয়।
- **Over-fetching/Under-fetching সমাধান**: ক্লায়েন্ট শুধু যা দরকার তাই রিকোয়েস্ট করে, extra ডেটা আসে না।
- **Schema এবং Type system**: স্ট্রংলি টাইপড schema (Query, Mutation, Subscription) ডিফাইন করা হয়।
- **Resolvers**: প্রতিটা field এর জন্য একটা resolver ফাংশন থাকে যা ডেটা fetch করে।
- **N+1 query problem**: একটা সাধারণ চ্যালেঞ্জ যেখানে nested field resolve করতে গিয়ে অনেক ডাটাবেস কল হয়ে যায় — এটা DataLoader/batching দিয়ে সমাধান করা হয়।
- **Subscriptions** দিয়ে real-time ডেটা আপডেট পাওয়া যায় (WebSocket এর উপর ভিত্তি করে)।

### আরও Advanced (Expert-Level)
- **Caching জটিলতা**: REST এ URL-based caching সহজ (HTTP cache headers ব্যবহার করে), কিন্তু GraphQL এ single endpoint ও dynamic query হওয়ায় caching জটিল — client-side caching (Apollo Client, Relay) দিয়ে সমাধান করা হয়, প্রায়ই `id` field দিয়ে normalized caching করে।
- **Query complexity/depth limiting**: ম্যালিশিয়াস বা অজান্তে খুব deeply nested query সার্ভারকে overload করতে পারে — query depth limit বা cost analysis দিয়ে এটা প্রতিরোধ করা হয়।
- **Persisted Queries**: প্রোডাকশনে পুরো query string বারবার না পাঠিয়ে একটা hash/ID পাঠানো হয় (সার্ভারে আগে থেকে রেজিস্টার করা), যা bandwidth বাঁচায় এবং সিকিউরিটি বাড়ায়।
- **Federation**: বড় সিস্টেমে একাধিক টিম আলাদা GraphQL schema ম্যানেজ করে, যেগুলো একটা unified gateway (Apollo Federation) এ combine করা হয়।

### Real-life Example
Facebook এর mobile app এ যদি শুধু ইউজারের নাম আর প্রোফাইল ছবি দরকার হয় (পুরো প্রোফাইল ডেটা না), GraphQL দিয়ে ঠিক সেই দুইটা field-ই রিকোয়েস্ট করা যায়, ফলে মোবাইল ডেটা বাঁচে। (GraphQL মূলত Facebook-ই তৈরি করেছিল এই কারণেই।)

### Practice Questions
```
1. GraphQL এবং REST API এর মধ্যে পার্থক্য কী?
2. Over-fetching এবং Under-fetching কী এবং GraphQL কীভাবে এটা সমাধান করে?
3. N+1 query problem কী এবং এটা কীভাবে সমাধান করবে?
4. GraphQL এ Query, Mutation এবং Subscription এর মধ্যে পার্থক্য কী?
5. GraphQL এর কোন কোন disadvantage আছে (যেমন caching জটিলতা)?
```

---

## ১৬. OAuth 2.0

### Basic
OAuth হলো একটা **authorization** ফ্রেমওয়ার্ক (authentication নয়) যা একটা অ্যাপ্লিকেশনকে ইউজারের পক্ষ থেকে অন্য একটা সার্ভিসের রিসোর্স অ্যাক্সেস করার permission দেয়, পাসওয়ার্ড শেয়ার না করেই।

### Advanced
OAuth 2.0 এর মূল উপাদান:
- **Resource Owner**: ইউজার নিজে
- **Client**: যে অ্যাপ্লিকেশন অ্যাক্সেস চাইছে
- **Authorization Server**: যেখান থেকে token ইস্যু হয় (যেমন Google's OAuth server)
- **Resource Server**: যেখানে actual ডেটা থাকে (যেমন Google Drive API)

**Grant types (flows)**:
- **Authorization Code Grant**: সবচেয়ে সিকিউর, web app এর জন্য (redirect + code exchange + token)
- **Client Credentials Grant**: server-to-server কমিউনিকেশনের জন্য
- **PKCE (Proof Key for Code Exchange)**: mobile/SPA অ্যাপের জন্য অতিরিক্ত সিকিউরিটি লেয়ার
- **Implicit Grant**: (এখন deprecated, কম secure)

Token flow: Client → Authorization Server (authorization code পায়) → Authorization Server (code exchange করে access token পায়) → Resource Server এ token দিয়ে অ্যাক্সেস করে।

### আরও Advanced (Expert-Level)
- **Scopes**: OAuth এ permission granular ভাবে নিয়ন্ত্রণ করা হয় scope দিয়ে (যেমন `read:email`, `write:calendar`) — অ্যাপকে শুধু প্রয়োজনীয় অ্যাক্সেস দেওয়া হয় (principle of least privilege)।
- **Refresh Token rotation**: প্রতিবার refresh token ব্যবহার করলে নতুন refresh token ইস্যু করা হয় এবং পুরনোটা invalidate করা হয় — token leak হলে ক্ষতি কমাতে সাহায্য করে।
- **OpenID Connect (OIDC)**: OAuth এর উপর তৈরি একটা identity layer যা authentication যোগ করে (OAuth শুধু authorization) — এখানে একটা extra **ID Token** (JWT ফরম্যাটে) থাকে ইউজারের identity তথ্য নিয়ে।
- **State parameter**: CSRF attack প্রতিরোধ করতে authorization request এ একটা random `state` value পাঠানো হয়, যা callback এ ভেরিফাই করা হয়।

### Real-life Example
"Sign in with Google" বাটন দিয়ে যখন তুমি কোনো থার্ড-পার্টি অ্যাপে লগইন করো, সেই অ্যাপ তোমার Google পাসওয়ার্ড কখনো দেখে না — Google শুধু একটা token দেয় যা দিয়ে সীমিত অনুমতি (যেমন শুধু ইমেইল আর নাম) পাওয়া যায়।

### Practice Questions
```
1. OAuth এবং Authentication এর মধ্যে পার্থক্য কী?
2. Authorization Code Grant flow স্টেপ বাই স্টেপ ব্যাখ্যা করো।
3. Access Token এবং Refresh Token এর মধ্যে পার্থক্য কী?
4. PKCE কী এবং এটা কেন দরকার SPA/Mobile app এ?
5. OAuth এবং JWT একসাথে কীভাবে কাজ করে?
```

---

## ১৭. JWT

### Basic
JWT (JSON Web Token) হলো একটা compact, self-contained টোকেন ফরম্যাট যা ইউজার সম্পর্কে তথ্য (claims) বহন করে এবং authentication/authorization এর জন্য ব্যবহৃত হয়।

### Advanced
JWT এর ৩টা অংশ (dot দিয়ে আলাদা করা, Base64Url encoded):
1. **Header**: algorithm এবং token type (`{"alg": "HS256", "typ": "JWT"}`)
2. **Payload**: claims — ইউজার ডেটা (যেমন `userId`, `exp`, `role`)
3. **Signature**: header + payload কে secret key দিয়ে সাইন করা, যাতে tamper detect করা যায়

- **Stateless authentication**: সার্ভারে সেশন স্টোর করার দরকার নেই — টোকেনেই সব তথ্য থাকে, যা scalability তে সুবিধা দেয়।
- **Signature verification** টোকেনের integrity নিশ্চিত করে, কিন্তু payload **encrypted নয়**, শুধু encoded — তাই sensitive data JWT তে রাখা উচিত না।
- **Expiration (`exp` claim)** ব্যবহার করে token এর মেয়াদ নিয়ন্ত্রণ করা হয়।
- **Revocation সমস্যা**: JWT stateless হওয়ায় logout/revoke করা কঠিন — এজন্য short expiry + refresh token pattern বা blacklist ব্যবহার করা হয়।

### আরও Advanced (Expert-Level)
- **Algorithm confusion attack**: `alg: none` বা RS256/HS256 এর মধ্যে confusion তৈরি করে সিগনেচার verification বাইপাস করার আক্রমণ — সার্ভারে সবসময় নির্দিষ্ট algorithm hardcode/whitelist করে রাখা উচিত।
- **JWE vs JWS**: JWS (JSON Web Signature) শুধু সাইন করা (integrity দেয়, কিন্তু payload readable), JWE (JSON Web Encryption) পুরো payload এনক্রিপ্ট করে — sensitive data এর জন্য JWE দরকার।
- **Token storage security**: JWT localStorage এ রাখলে XSS attack এ চুরি হতে পারে, HttpOnly cookie তে রাখলে বেশি নিরাপদ কিন্তু CSRF handle করতে হয় — trade-off বোঝা জরুরি।
- **Key rotation (`kid` claim)**: signing key পরিবর্তন করার সময় একাধিক key সাপোর্ট করতে হয়, `kid` (Key ID) হেডার দিয়ে কোন key ব্যবহৃত হয়েছে তা identify করা হয়।

### Real-life Example
একটা API-তে লগইন করার পর সার্ভার একটা JWT দেয়, এরপর প্রতিটা রিকোয়েস্টে `Authorization: Bearer <token>` হেডারে সেটা পাঠানো হয় — সার্ভার ডাটাবেসে সেশন চেক না করেই টোকেন verify করে বুঝে যায় ইউজার কে এবং কী পারমিশন আছে।

### Practice Questions
```
1. JWT এর তিনটা অংশ কী কী এবং প্রতিটার কাজ কী?
2. JWT কেন stateless authentication এর জন্য জনপ্রিয়?
3. JWT কি এনক্রিপ্টেড? এটা কি secure ডেটা রাখার জন্য নিরাপদ?
4. JWT কীভাবে revoke/invalidate করবে (যেহেতু এটা stateless)?
5. Session-based authentication এবং JWT-based authentication এর মধ্যে পার্থক্য কী?
```

---

## ১৮. CORS

### Basic
CORS (Cross-Origin Resource Sharing) হলো একটা ব্রাউজার সিকিউরিটি মেকানিজম যা নিয়ন্ত্রণ করে কোন origin (domain/port/protocol) থেকে অন্য origin এর রিসোর্স অ্যাক্সেস করা যাবে কিনা।

### Advanced
- **Same-Origin Policy**: ডিফল্টভাবে ব্রাউজার শুধু একই origin (protocol + domain + port) থেকে রিসোর্স অ্যাক্সেসের অনুমতি দেয়। CORS সেটাকে নিয়ন্ত্রিতভাবে শিথিল করে।
- **Preflight request**: `PUT`, `DELETE`, বা custom header যুক্ত রিকোয়েস্টের আগে ব্রাউজার automatic একটা `OPTIONS` রিকোয়েস্ট পাঠায় জানতে সার্ভার এই cross-origin রিকোয়েস্ট allow করে কিনা।
- **Headers** যা CORS নিয়ন্ত্রণ করে: `Access-Control-Allow-Origin`, `Access-Control-Allow-Methods`, `Access-Control-Allow-Headers`, `Access-Control-Allow-Credentials`।
- **Simple requests** (GET, POST with standard content-type) preflight ছাড়াই সরাসরি যায়।
- CORS শুধু ব্রাউজার-level protection — server-to-server কল CORS দিয়ে বাধাপ্রাপ্ত হয় না।

### আরও Advanced (Expert-Level)
- **Credentialed requests**: cookie/authorization header সহ cross-origin রিকোয়েস্ট পাঠাতে `Access-Control-Allow-Credentials: true` এবং `Access-Control-Allow-Origin` এ wildcard (`*`) এর বদলে নির্দিষ্ট origin লাগে — দুটো একসাথে wildcard এ কাজ করে না।
- **CORS vs CSRF**: এই দুটো ভিন্ন সমস্যা সমাধান করে — CORS ব্রাউজারকে বলে কোন origin থেকে response পড়া যাবে, CSRF token নিশ্চিত করে রিকোয়েস্টটা legitimate সোর্স থেকে এসেছে। একটা থাকলে অন্যটা দরকার নেই এমন না।
- **Wildcard vs specific origin trade-off**: `*` ব্যবহার করলে যেকোনো সাইট রিসোর্স অ্যাক্সেস করতে পারবে (public API তে ঠিক আছে), কিন্তু sensitive ডেটার ক্ষেত্রে নির্দিষ্ট origin whitelist করা উচিত।
- **CORS misconfigurations security risk**: ভুলভাবে সব origin কে allow করা (বিশেষত credentials সহ) sensitive ডেটা leak করার একটা সাধারণ vulnerability।

### Real-life Example
তোমার ফ্রন্টএন্ড `https://myapp.com` এ চলছে কিন্তু API আছে `https://api.myapp.com` এ — ব্রাউজার ডিফল্টভাবে এই cross-origin কল ব্লক করবে, যতক্ষণ না API সার্ভার `Access-Control-Allow-Origin: https://myapp.com` হেডার পাঠায়।

### Practice Questions
```
1. CORS কী এবং কেন এটা দরকার?
2. Same-Origin Policy কী?
3. Preflight request কী এবং এটা কখন trigger হয়?
4. CORS error কীভাবে ফিক্স করবে?
5. CORS কি সার্ভার সিকিউরিটি ফিচার নাকি ব্রাউজার সিকিউরিটি ফিচার?
```

---

## ১৯. Cookies

### Basic
Cookie হলো ছোট একটা key-value ডেটা যা সার্ভার ব্রাউজারে স্টোর করে রাখে, এবং প্রতিটা পরবর্তী রিকোয়েস্টে সেটা স্বয়ংক্রিয়ভাবে সার্ভারে ফিরে যায় — যা session, preference বা tracking এর জন্য ব্যবহৃত হয়।

### Advanced
- **Cookie attributes**:
  - `HttpOnly`: JavaScript থেকে অ্যাক্সেস করা যায় না (XSS প্রতিরোধ করে)
  - `Secure`: শুধু HTTPS কানেকশনে পাঠানো হয়
  - `SameSite` (Strict/Lax/None): CSRF আক্রমণ প্রতিরোধে ব্যবহৃত হয় — কোন cross-site রিকোয়েস্টে cookie পাঠানো হবে তা নিয়ন্ত্রণ করে
  - `Expires`/`Max-Age`: কতক্ষণ cookie বৈধ থাকবে
- Cookie সাইজ সীমিত (~4KB), তাই বড় ডেটার জন্য উপযুক্ত না।
- **First-party vs Third-party cookies**: থার্ড-পার্টি cookies (advertising tracking এ ব্যবহৃত) এখন privacy কারণে ব্রাউজারে বেশি রেস্ট্রিক্টেড হচ্ছে।

### আরও Advanced (Expert-Level)
- **Cookie prefixes (`__Secure-`, `__Host-`)**: ব্রাউজার-enforced নামকরণ কনভেনশন যা নিশ্চিত করে cookie নির্দিষ্ট শর্ত (HTTPS, নো domain attribute) মেনে সেট হয়েছে কিনা — অতিরিক্ত protection layer।
- **Third-party cookie deprecation**: Chrome-সহ প্রধান ব্রাউজারগুলো থার্ড-পার্টি cookie ধাপে ধাপে বন্ধ করছে privacy কারণে — এর বিকল্প হিসেবে Privacy Sandbox/Topics API এর মতো টেকনোলজি আসছে।
- **CSRF এবং cookie এর সম্পর্ক**: ব্রাউজার স্বয়ংক্রিয়ভাবে cookie পাঠায় বলেই CSRF attack সম্ভব হয় — `SameSite=Strict/Lax` এই ঝুঁকি অনেকটা কমায়।
- **Cookie জার সাইজ সীমা**: প্রতি ডোমেইনে সাধারণত ৫০টা পর্যন্ত cookie এবং ব্রাউজার প্রতি মোট ~৪KB/cookie সীমা থাকে — বেশি ডেটার জন্য server-side session বা localStorage বিবেচনা করা উচিত।

### Real-life Example
তুমি একটা ওয়েবসাইটে লগইন করার পর পেজ রিফ্রেশ করলেও লগইন অবস্থায় থাকো — এটা সম্ভব হয় কারণ সার্ভার একটা session cookie সেট করে দিয়েছে যা প্রতি রিকোয়েস্টে যাচাই হয়।

### Practice Questions
```
1. HttpOnly এবং Secure cookie attribute এর কাজ কী?
2. SameSite cookie attribute কীভাবে CSRF আক্রমণ প্রতিরোধ করে?
3. Cookie এবং LocalStorage এর মধ্যে পার্থক্য কী?
4. Session cookie এবং persistent cookie এর মধ্যে পার্থক্য কী?
```

---

## ২০. Session Management

### Basic
Session management হলো ব্যবহারকারীর লগইন স্টেট বা অ্যাক্টিভিটি একাধিক HTTP রিকোয়েস্ট জুড়ে ট্র্যাক রাখার প্রক্রিয়া — যেহেতু HTTP নিজে stateless।

### Advanced
দুইটা প্রধান approach:
1. **Server-side session (stateful)**:
   - সার্ভার একটা session ID জেনারেট করে, সেটা cookie তে ক্লায়েন্টে পাঠায়।
   - Session ডেটা সার্ভারে (memory, Redis, ডাটাবেসে) স্টোর হয়।
   - স্কেল করতে গেলে **sticky sessions** বা **shared session store** (Redis) দরকার হয় multi-server environment এ।
2. **Client-side / Token-based session (stateless)**:
   - JWT এর মতো টোকেন ক্লায়েন্টে রাখা হয়, সার্ভারে কিছু স্টোর করতে হয় না।
   - Horizontal scaling সহজ, কিন্তু token revocation জটিল।

**Session security concerns**:
- **Session fixation**: আক্রমণকারী আগে থেকে একটা session ID সেট করে দিয়ে পরে সেটা hijack করে — লগইনের পর নতুন session ID জেনারেট করে এটা প্রতিরোধ করা হয়।
- **Session hijacking**: cookie চুরি হয়ে অন্য কেউ সেশন ব্যবহার করা — `HttpOnly`, `Secure`, `SameSite` cookie দিয়ে প্রতিরোধ করা হয়।
- **Session timeout**: নির্দিষ্ট সময় পর সেশন expire করা (idle timeout, absolute timeout)।

### আরও Advanced (Expert-Level)
- **Distributed session store**: multi-server আর্কিটেকচারে Redis/Memcached এর মতো centralized, fast in-memory store ব্যবহার করা হয় যাতে যেকোনো সার্ভার যেকোনো ইউজারের সেশন অ্যাক্সেস করতে পারে (sticky session এর বিকল্প)।
- **Sticky session এর trade-off**: লোড ব্যালেন্সার একই ইউজারকে বারবার একই সার্ভারে পাঠায় — সহজ কিন্তু সার্ভার ক্র্যাশ করলে সেশন হারিয়ে যায় এবং uneven load distribution হতে পারে।
- **Hybrid approach**: অনেক প্রোডাকশন সিস্টেম JWT (stateless, দ্রুত ভেরিফিকেশন) এবং একটা server-side revocation list/refresh token store (stateful অংশ) একসাথে ব্যবহার করে — best of both worlds পাওয়ার চেষ্টা।
- **Session vs Token তুলনায় consistency**: distributed session store এ latency/consistency trade-off থাকে (Redis replica lag), যা race condition তৈরি করতে পারে high-traffic সিস্টেমে।

### Real-life Example
অনলাইন ব্যাংকিং অ্যাপে কিছুক্ষণ কোনো কাজ না করলে "Session expired, please login again" মেসেজ আসে — এটা idle timeout এর উদাহরণ, যা সিকিউরিটির জন্য গুরুত্বপূর্ণ, বিশেষত পাবলিক কম্পিউটারে।

### Practice Questions
```
1. Server-side session এবং Client-side (token-based) session এর মধ্যে পার্থক্য কী?
2. Session fixation attack কী এবং কীভাবে প্রতিরোধ করা যায়?
3. Multi-server environment এ session management কীভাবে scale করবে (sticky session vs shared store)?
4. Session hijacking প্রতিরোধ করার উপায়গুলো কী কী?
5. Stateless authentication (JWT) কীভাবে session scaling সমস্যা সমাধান করে?
```

---

## 📌 বোনাস: সব টপিক নিয়ে সংযুক্ত ইন্টারভিউ প্রশ্ন (System Design Level)

```markdown
1. তুমি যদি একটা রিয়েল-টাইম চ্যাট অ্যাপ্লিকেশন ডিজাইন করো, WebSocket নাকি SSE নাকি polling ব্যবহার করবে — এবং কেন?
2. একটা পাবলিক API ডিজাইন করার সময় REST নাকি GraphQL বেছে নেবে — trade-off গুলো ব্যাখ্যা করো।
3. Authentication এর জন্য session-cookie নাকি JWT ব্যবহার করবে একটা মাল্টি-সার্ভার মাইক্রোসার্ভিস আর্কিটেকচারে — কেন?
4. একটা ওয়েবসাইটের লোড টাইম কীভাবে কমাবে DNS, CDN, HTTP/2 বা HTTP/3 ব্যবহার করে?
5. HTTPS/TLS কীভাবে ম্যান-ইন-দ্য-মিডল অ্যাটাক প্রতিরোধ করে — পুরো ফ্লো ব্যাখ্যা করো।
6. OAuth এবং JWT একসাথে কীভাবে একটা secure authentication system তৈরি করে?
7. CORS error পেলে একজন ডেভেলপার হিসেবে তুমি কীভাবে debug করবে?
```

---

## 📚 পড়াশোনার পরামর্শ

- প্রতিটা টপিক নিজে diagram এঁকে বোঝার চেষ্টা করো (বিশেষ করে TCP handshake, TLS handshake, OAuth flow)।
- Wireshark দিয়ে বাস্তব প্যাকেট ক্যাপচার করে TCP/HTTP দেখা প্র্যাকটিস করলে concept আরও পাকা হবে।
- Browser DevTools এর Network Tab ব্যবহার করে real HTTP request/response, cookie, header — এগুলো নিজে দেখো।

---

## 📋 ২০টি নেটওয়ার্কিং টপিকের রিভিশন ও চেকলিস্ট

| # | টপিক | লেয়ার / ধরন | মূল কাজ ও আর্কিটেকচারাল ডিসিশন | বাস্তব প্রোডাকশন ব্যবহার |
|---|---|---|---|---|
| **১** | **TCP/IP** | Transport | 3-way handshake, Reliable & Ordered delivery, Flow/Congestion Control | ওয়েব ব্রাউজিং, ডাটাবেজ কানেকশন, ফাইল ডাউনলোড |
| **২** | **UDP** | Transport | Connectionless, Low overhead (8 byte header), No retransmission | লাইভ ভিডিও স্ট্রিমিং, Zoom কল, অনলাইন গেমিং, DNS |
| **৩** | **HTTP/1.1** | Application | Request-Response, Persistent connection (keep-alive), HOL blocking | ট্র্যাডিশনাল ওয়েব অ্যাপ্লিকেশন ও ব্যাকএন্ড সার্ভিস |
| **৪** | **HTTP/2** | Application | Binary framing, Multiplexing, HPACK Header Compression, Server Push | আধুনিক ওয়েব সাইট, হাই-পারফরম্যান্স ফ্রন্টএন্ড |
| **৫** | **HTTP/3** | Application/QUIC | UDP-based QUIC, Zero-RTT connection, No TCP HOL blocking | Google, YouTube, Facebook মোবাইল অ্যাপ ট্রাফিক |
| **৬** | **QUIC** | Transport | UDP + TLS 1.3 + Built-in Congestion Control & Connection Migration | দুর্বল মোবাইল নেটওয়ার্কে নিরবচ্ছিন্ন ব্রাউজিং |
| **৭** | **DNS** | Application/Edge | Domain Name to IP Translation, Root/TLD/Authoritative Resolver | গ্লোবাল ডোমেইন রাউটিং, জিওগ্রাফিক ট্রাফিক ডিস্ট্রিবিউশন |
| **৮** | **CDN** | Edge/Caching | Edge Server Caching, Origin Protection, Static & Dynamic Acceleration | Netflix ভিডিও ক্যাশিং, Cloudflare DDoS সুরক্ষা |
| **৯** | **SSL/TLS** | Security/Transport | Asymmetric Handshake (Key Exchange) + Symmetric Data Encryption | সিকিউর ডেটা ট্রান্সমিশন, ম্যান-ইন-দ্য-মিডল অ্যাটাক প্রতিরোধ |
| **১০** | **HTTPS** | Security/App | HTTP over TLS (Port 443), Confidentiality, Integrity, Authentication | ব্যাংকিং, ই-কমার্স ও আধুনিক সব সুরক্ষিত ওয়েব পোর্টাল |
| **১১** | **WebSocket** | Real-time | Full-duplex bidirectional persistent connection over single TCP socket | রিয়েল-টাইম চ্যাট (WhatsApp Web), লাইভ স্টক মার্কেট |
| **১২** | **SSE** | Real-time | Server-to-client unidirectional continuous stream over standard HTTP | লাইভ স্কোর আপডেট, নোটিফিকেশন ফিড, স্টক টিকার |
| **১৩** | **gRPC** | RPC/API | HTTP/2 Multiplexing + Protocol Buffers (Binary), 4 Streaming modes | মাইক্রোসার্ভিস ইন্টার-সার্ভিস হাই-স্পিড কমিউনিকেশন |
| **১৪** | **REST API** | API Style | Stateless, Resource-oriented (URI), Standard HTTP Verbs, Idempotent | পাবলিক এপিআই, CRUD ব্যাকএন্ড সার্ভিস |
| **১৫** | **GraphQL** | API Style | Single endpoint (`/graphql`), Client-specified payload query, No overfetching | ফেসবুক মোবাইল অ্যাপ, জটিল নেস্টেড ডাটা এগ্রিগেশন |
| **১৬** | **OAuth 2.0** | Authorization | Delegation of authority via Token, Authorization Code with PKCE | "Sign in with Google", থার্ড-পার্টি অ্যাপ পারমিশন |
| **১৭** | **JWT** | Auth Token | Compact, self-contained, digitally signed (Header.Payload.Signature) | Stateless মাইক্রোসার্ভিস অথেনটিকেশন ও RBAC |
| **১৮** | **CORS** | Browser Security | Same-Origin Policy relaxation via Headers, Preflight `OPTIONS` check | ফ্রন্টএন্ড SPA (`myapp.com`) থেকে API (`api.myapp.com`) কল |
| **১৯** | **Cookies** | Browser State | Small key-value storage with `HttpOnly`, `Secure`, `SameSite` attributes | সেশন আইডি স্টোরেজ, ট্র্যাকিং, CSRF সুরক্ষা |
| **২০** | **Session Management**| State Management| Server-side session (Redis store) বনাম Client-side (JWT stateless) | ইউজার লগইন স্টেট, ইনঅ্যাক্টিভিটি টাইমআউট, সেশন ফিক্সেশন প্রিভেনশন |

---

[← Fundamentals](01-system-design-fundamentals.md) · [Roadmap](../ROADMAP.md) · [Databases →](03-databases.md)
