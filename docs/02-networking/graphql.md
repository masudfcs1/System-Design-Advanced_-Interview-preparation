# GraphQL

[Back to Networking topics](README.md) · [Module guide](../02-networking.md)

## 📌 Learning Checklist
- [ ] Understand the fundamental philosophy of GraphQL (Client-driven query language).
- [ ] Learn how GraphQL solves Over-fetching and Under-fetching.
- [ ] Master the Schema Definition Language (SDL): Queries, Mutations, Subscriptions, and Types.
- [ ] Understand the dreaded $N+1$ Problem and how DataLoader resolves it via batching and caching.
- [ ] Analyze GraphQL Security Vulnerabilities: Query Depth Attacks, Query Complexity, and DoS.
- [ ] Defend GraphQL vs REST in Staff-level API architecture interviews.

---

## 📖 Deep Dive Notes

### 1. সহজ সংজ্ঞা ও Intuitive Mental Model

২০১২ সালে ফেসবুক (Meta) কর্তৃক উদ্ভাবিত এবং ২০১৫ সালে ওপেন-সোর্সকৃত **GraphQL** হলো এপিআই-এর জন্য একটি ডিক্লেয়ারেটিভ কুয়েরি ল্যাঙ্গুয়েজ (Query Language) এবং সার্ভার-সাইড রানটাইম। 

প্রথাগত REST API-তে যেখানে সার্ভার ঠিক করত ক্লায়েন্ট কী ডেটা পাবে, GraphQL-এ পুরো নিয়ন্ত্রণ চলে আসে **ক্লায়েন্টের হাতে**—ক্লায়েন্ট সুনির্দিষ্টভাবে বলে দেয় তার কোন কোন ফিল্ড লাগবে, এবং সার্ভার হুবহু সেই নির্দিষ্ট ডেটাই রিটার্ন করে—এক বাইট বেশিও না, এক বাইট কমও না।

```
[ Client GraphQL Query ]
query {
  user(id: "42") {
    name
    email
    orders(limit: 2) {
      id
      total
    }
  }
}

[ Server JSON Response ]
{
  "data": {
    "user": {
      "name": "Rahim",
      "email": "rahim@example.com",
      "orders": [
        { "id": "101", "total": 500 },
        { "id": "102", "total": 1200 }
      ]
    }
  }
}
```

> 🧠 **Intuitive Mental Model (বুফে রেস্তোরাঁ বনাম ফিক্সড সেট মেন্যু):**
> - **REST API (ফিক্সড সেট মেন্যু):** রেস্তোরাঁর ওয়েটার এসে আপনাকে একটি ১,২০০ টাকার সেট মেন্যু ধরিয়ে দিল যাতে বার্গার, ফ্রাই, সালাদ, কোল্ড ড্রিঙ্কস এবং মিষ্টি আছে। আপনার হয়তো কেবল বার্গার আর ফ্রাই খেতে ইচ্ছে করছে, কিন্তু আপনাকে পুরো প্যাকেজটাই টেবিল ভর্তি করে নিতে হচ্ছে (**Over-fetching**)। আবার মিষ্টি খাওয়ার জন্য আপনাকে পাশের মিষ্টির দোকানে আলাদা অর্ডার দিতে যেতে হচ্ছে (**Under-fetching**)।
> - **GraphQL (বুফে রেস্তোরাঁ):** আপনি একটি প্লেট হাতে নিয়ে বুফে কাউন্টারে দাঁড়ালেন (**The GraphQL Endpoint**)। আপনি বাটিতে সুনির্দিষ্টভাবে যতটুকু সালাদ চান নিলেন, আর ১টি কাবাব নিলেন। আপনি ঠিক যা চেয়েছেন প্লেটে তাই আছে—অপ্রয়োজনীয় কোনো অতিরিক্ত খাবার বা বারবার অন্য দোকানে যাওয়ার কোনো প্রয়োজন নেই!

---

### 2. Over-fetching এবং Under-fetching-এর চিরতরে অবসান

```
Over-fetching (REST Problem):
GET /api/v1/users/42
Returns: { id, name, email, address, ssn, created_at, preferences, phone, zip, ... }
(মোবাইল স্ক্রিনে শুধু নাম লাগবে, অথচ ব্যাকএন্ড ৫০টি অপ্রয়োজনীয় ফিল্ড পাঠিয়ে ৫০ কেবি ব্যান্ডউইথ নষ্ট করল!)

Under-fetching (REST Problem):
Screen needs User Name + Orders + Reviews:
1. GET /api/v1/users/42      ──► (Round trip 1: 100ms)
2. GET /api/v1/users/42/orders ──► (Round trip 2: 100ms)
3. GET /api/v1/users/42/reviews ─► (Round trip 3: 100ms)
Total Latency = 300ms! (৩টি আলাদা নেটওয়ার্ক রাউন্ড ট্রিপ!)

GraphQL Solution (1 Call, Perfect Payload):
POST /graphql
query { user(id: 42) { name orders { id } reviews { rating } } }
Total Latency = 100ms! (১টি মাত্র কলে সমস্ত নেস্টেড ডেটা চলে আসলো!)
```

---

### 3. The $N+1$ Problem এবং Facebook DataLoader

GraphQL-এর সবচেয়ে মারাত্মক ও কুখ্যাত পারফরম্যান্স ফাঁদ হলো **$N+1$ Database Query Problem**:

```
Query:
query {
  posts(limit: 10) {     <-- 1 Database Query: SELECT * FROM posts LIMIT 10;
    author {             <-- For EACH of the 10 posts, a separate query is executed!
      name               <-- 10 separate queries: SELECT * FROM users WHERE id = post.author_id;
    }
  }
}
Total DB Queries = 1 + 10 = 11 Queries! (১০০০ পোস্ট হলে ১০০১টি কুয়েরি ডাটাবেজ ক্র্যাশ করাবে!)
```

#### সমাধান: DataLoader (Batching & In-Memory Caching)
- ফেসবুকের উদ্ভাবিত **DataLoader** মেকানিজম একটি একক ইভেন্ট লুপ টিকের (Tick) মধ্যে সমস্ত ইনকামিং আইডিগুলোকে বাফার করে একত্রিত (Batch) করে।
- ১০টি আলাদা কুয়েরি চালানোর বদলে সে ডাটাবেজে একটিমাত্র অপটিমাইজড SQL ইন-কুয়েরি চালায়:
  ```sql
  SELECT * FROM users WHERE id IN (1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
  ```
- ফলে ১০০১টি কুয়েরি নিমেষের মধ্যে মাত্র **২টি কুয়েরিতে** নেমে আসে!

---

### 4. GraphQL Security Vulnerabilities (ডেপথ অ্যাটাক ও DoS)

REST-এ এন্ডপয়েন্টগুলো ফিক্সড থাকায় হ্যাকাররা সহজে সার্ভারকে আনলিমিটেড কুয়েরি দিয়ে পরাস্ত করতে পারে না। কিন্তু GraphQL-এ ক্লায়েন্ট নিজেই কুয়েরি রচনা করে:

```graphql
# ❌ DANGEROUS RECURSIVE DEPTH ATTACK:
query EvilQuery {
  author(id: 1) {
    posts {
      author {
        posts {
          author {
            posts { ... } # ৫০ লেভেল নেস্টিং!
          }
        }
      }
    }
  }
}
# একটি মাত্র রিকোয়েস্টেই সার্ভারের ডাটাবেজ ও সিপিইউ ১০০% জ্যাম হয়ে শতভাগ ক্র্যাশ করবে!
```

#### প্রোডাকশন সিকিউরিটি কন্ট্রোল:
1. **Query Depth Limiting:** সর্বোচ্চ নেস্টিং লেভেল (যেমন: ৫ বা ৬ এর বেশি নয়) বেঁধে দেওয়া। এর বেশি গভীর হলে কুয়েরি ড্রপ করা।
2. **Query Complexity Analysis:** প্রতিটি ফিল্ডের জন্য একটি কস্ট পয়েন্ট নির্ধারণ করা। মোট পয়েন্ট যদি ১০০০ ছাড়িয়ে যায়, রিকোয়েস্ট রিজেক্ট করা।
3. **Persisted Queries (Production Gold Standard):** প্রোডাকশন ক্লায়েন্ট রানটাইমে কোনো ওপেন কুয়েরি পাঠাতে পারবে না! বিল্ডের সময় সমস্ত কুয়েরির একটি SHA-256 হ্যাশ তৈরি করে সার্ভারে সেভ রাখা হবে। ক্লায়েন্ট শুধু ওই হ্যাশ পাঠাবে (`{ id: "a8f9c2" }`), ফলে বহিরাগত কোনো ক্ষতিকর কুয়েরি রান করা অসম্ভব।

---

### 5. Alternatives & Comparison Matrix

| বৈশিষ্ট্য | GraphQL | REST API | gRPC |
|---|---|---|---|
| **ডেটা মডেল** | গ্রাফ/রিলেশনাল অবজেক্টস | রিসোর্স-ভিত্তিক (URI) | সার্ভিস-ভিত্তিক (RPC) |
| **ওভার-ফেচিং** | ✅ **শূন্য (ক্লায়েন্ট ফিল্ড বেছে নেয়)** | ❌ সাধারণ সমস্যা | ❌ সাধারণ সমস্যা |
| **সিডিএন এজ ক্যাশিং** | ❌ খুব কঠিন (সবকিছু POST-এ যায়) | ✅ **অত্যন্ত সহজ (HTTP Cache-Control)** | ❌ খুব কঠিন |
| **টাইপিং ও স্কিমা** | **বাধ্যতামূলক (SDL)** | ঐচ্ছিক (OpenAPI) | **বাধ্যতামূলক (Protobuf)** |
| **আর্কিটেকচারাল জটিলতা** | উচ্চ (DataLoader, Depth limiter লাগে) | কম | মাঝারি |
| **আদর্শ ক্ষেত্র** | **জটিল মোবাইল ও রিচ সিঙ্গেল পেজ ইউআই** | পাবলিক এপিআই ও স্ট্যাটিক এসেট | মাইক্রোসার্ভিস ব্যাকবোন |

---

### 6. Senior / Staff Engineer Interview Defense

> **ইন্টারভিউয়ার:** *"GraphQL-এ সাধারণ HTTP ক্যাশিং কাজ করে না কেন? প্রোডাকশনে কোটি কোটি ক্লায়েন্টের জন্য GraphQL এপিআই-এর রেসপন্স ক্যাশিং কীভাবে আর্কিটেক্ট করবেন?"*
>
> 💡 **Staff-Level উত্তরের কাঠামো:**
> "ঐতিহ্যবাহী REST-এ প্রতিটি স্বতন্ত্র রিসোর্সের জন্য একটি ইউনিক গ্লোবাল ইউআরএল পাথ (`/api/v1/products/123`) এবং HTTP `GET` মেথড থাকে, যা সিডিএন (Cloudflare) ও ব্রাউজারকে সহজে ইউআরএল-কে ক্যাশ কি (Cache Key) হিসেবে ব্যবহার করতে দেয়।
> কিন্তু GraphQL-এ সমস্ত ক্লায়েন্ট রিকোয়েস্ট একটিমাত্র একক এন্ডপয়েন্টে যায় (`POST /graphql`) এবং কুয়েরি থাকে বডির ভেতর। HTTP স্পেক অনুযায়ী POST রিকোয়েস্ট ডিফল্টভাবে নন-ক্যাশেবল।
> প্রোডাকশনে GraphQL স্কেল করতে আমরা ৩টি আধুনিক ক্যাশিং স্তর প্রয়োগ করব:
> 1. **Automated Persisted Queries (APQ):** ক্লায়েন্ট পুরো কুয়েরি না পাঠিয়ে কুয়েরির SHA-256 হ্যাশ একটি HTTP `GET` রিকোয়েস্টে পাঠায় (`GET /graphql?hash=a8f9c2...`)। এটি প্রোটোকলকে পুনরায় GET-এ ফিরিয়ে আনে, ফলে সিডিএন এজ ক্যাশ স্বাভাবিকভাবে কার্যকর হয়!
> 2. **Client-side Normalized In-Memory Cache (Apollo Client / Relay):** ক্লায়েন্ট ব্রাউজার প্রতিবার সার্ভারে কল না করে রেসপন্সের অবজেক্ট আইডি (`__typename:id`) দিয়ে লোকাল মেমোরিতে নরম্যালাইজড গ্রাফ ক্যাশ রাখে।
> 3. **DataLoader ও লেয়ার্ড ব্যাকএন্ড ক্যাশিং:** ব্যাকএন্ড রিসলভারে Redis ও DataLoader ব্যবহার করে ডাটাবেজ কুয়েরিগুলোকে মাইক্রোসেকেন্ডে ক্যাশ থেকে সার্ভ করব।"

---

## 📝 Practice Questions

```markdown
### Basic Practice Questions
1. GraphQL কী এবং এটি কোন কোম্পানি প্রথম তৈরি করেছিল?
2. Over-fetching এবং Under-fetching বলতে কী বোঝায়?
3. GraphQL-এ Query, Mutation এবং Subscription-এর কাজ কী?
4. Schema Definition Language (SDL) কী?
5. কেন GraphQL-এ সমস্ত রিকোয়েস্ট সাধারণত HTTP POST মেথডে পাঠানো হয়?

### Intermediate Practice Questions
6. GraphQL-এর $N+1$ Problem কী এবং এটি কীভাবে ডাটাবেজ পারফরম্যান্স ধসিয়ে দেয়?
7. Facebook DataLoader কীভাবে ব্যাচিং ও মেমোইজেশন ক্যাশিংয়ের মাধ্যমে $N+1$ সমস্যা সমাধান করে?
8. Query Depth Attack কী এবং কীভাবে সার্ভারকে রিকার্সিভ অ্যাটাক থেকে রক্ষা করবেন?
9. কেন ঐতিহ্যবাহী HTTP/CDN ক্যাশিং GraphQL-এর ক্ষেত্রে সহজে কাজ করে না?
10. Automated Persisted Queries (APQ) কী এবং এটি কীভাবে GraphQL-কে HTTP GET ক্যাশিং সুবিধা দেয়?

### Advanced / Staff-Level Questions
11. GraphQL Federation (Apollo Federation): একাধিক স্বায়ত্তশাসিত মাইক্রোসার্ভিসের সাব-গ্রাফগুলোকে কীভাবে একটি একক ইউনিফাইড সুপার-গ্রাফ গেটওয়েতে সমন্বয় করবেন?
12. Schema Stitching বনাম GraphQL Federation—মাইক্রোসার্ভিস টিম স্কেলিংয়ে এদের মধ্যকার স্থাপত্যিক পার্থক্য কী?
13. রিয়েল-টাইম আপডেটের জন্য GraphQL Subscriptions পরিচালনা করার সময় লাখ লাখ ক্লায়েন্টের জন্য WebSocket সকেটের মেমোরি ও কানেকশন স্কেলিং কীভাবে করবেন?
14. ফিল্ড-লেভেল অথরাইজেশন (Field-Level Authorization) এবং রোল-বেসড সিকিউরিটি GraphQL রিসলভার আর্কিটেকচারে কীভাবে ক্লিয়ারলি ইমপ্লিমেন্ট করবেন?
15. REST এপিআই-এর ওপরে একটি GraphQL র্যাপার লেয়ার (BFF) বসানোর সময় রেসপন্স ল্যাটেন্সি এবং এরর ট্র্যাকিং কীভাবে অপ্টিমাইজ করবেন?
```

---

## 🔑 Answer Key & Self-Test Evaluation

<details>
<summary>👉 <b>Answer Key ও সমাধান দেখতে এখানে ক্লিক করুন</b></summary>

1. **সংজ্ঞা:** এপিআই-এর জন্য একটি ডিক্লেয়ারেটিভ কুয়েরি ল্যাঙ্গুয়েজ ও রানটাইম; এটি ফেসবুক (Meta) তৈরি করেছে।
2. **Over/Under-fetching:** Over-fetching মানে প্রয়োজনের চেয়ে বেশি অপ্রয়োজনীয় ফিল্ড আসা; Under-fetching মানে ১টি পেজের ডেটা পেতে বারবার একাধিক আলাদা কল করতে হওয়া।
3. **Operations:** Query হলো ডেটা পড়া (GET); Mutation হলো ডেটা পরিবর্তন করা (POST/PUT); Subscription হলো রিয়েল-টাইম লাইভ ইভেন্ট শোনা (WebSocket)।
4. **SDL:** টাইপ, ফিল্ড এবং অপারেশন সংজ্ঞায়িত করার মানব-পাঠযোগ্য স্কিমা সিনট্যাক্স।
5. **POST ব্যবহার:** কুয়েরি টেক্সট অনেক বড় ও নেস্টেড হতে পারে, যা ব্রাউজারের ইউআরএল লেন্থ লিমিট অতিক্রম করতে পারে।
6. **N+1 Problem:** ১টি কুয়েরিতে তালিকা এনে তালিকার প্রতিটি উপাদানের ভেতরের সম্পর্কের জন্য অতিরিক্ত N বার ডাটাবেজ কুয়েরি চালানো।
7. **DataLoader:** একটি ইভেন্ট লুপে আসা সমস্ত সিঙ্গেল কুয়েরি বাফার করে একটি একক `WHERE id IN (...)` এসকিউএল কুয়েরি চালায়।
8. **Depth Attack:** গভীরভাবে নেস্টেড রিকার্সিভ কুয়েরি পাঠিয়ে সার্ভার হ্যাং করা। সমাধান: কুয়েরি ডেপথ লিমিট প্লাগইন দিয়ে সর্বোচ্চ ৫ স্তরে সীমাবদ্ধ রাখা।
9. **CDN Caching ব্যর্থতা:** সমস্ত রিকোয়েস্ট একটিমাত্র ইউআরএলে (`/graphql`) এবং POST বডিতে যায়, ফলে সিডিএন ইউআরএল দেখে ক্যাশ কি তৈরি করতে পারে না।
10. **APQ:** কুয়েরির টেক্সট না পাঠিয়ে কুয়েরির হ্যাশ GET মেথডে পাঠানো, ফলে সিডিএন এজ ইউআরএল হ্যাশ দেখে রেসপন্স ক্যাশ করতে পারে।
11. **Apollo Federation:** প্রতিটি মাইক্রোসার্ভিস তার নিজস্ব সত্ত্বা (Entity) এক্সপোজ করে এবং গেটওয়ে কোনো একক পয়েন্ট অফ ফেইলিউর ছাড়া রানটাইমে ডাইনামিকালি এক্সিকিউশন প্ল্যান তৈরি করে রেজল্ভ করে।
12. **Stitching vs Federation:** Stitching কোড লিখে সেন্ট্রাল গেটওয়েতে ম্যানুয়ালি স্কিমা জুড়তে হয়; Federation প্রতিটি সার্ভিস নিজেই ডিক্লেয়ার করে তারা কোন কোন ফিল্ডের মালিক (ডিস্ট্রিবিউটেড ওনারশিপ)।
13. **Subscription Scaling:** গেটওয়েগুলো সাবস্ক্রিপশন সকেট ধরে রাখে এবং পেছনের কাফকা/রেডিস পাব-সাব থেকে ইভেন্ট পেয়ে ফিল্টার করে নির্দিষ্ট ক্লায়েন্টে পুশ করে।
14. **Field Authorization:** স্কিমা ডিরেক্টিভ (`@auth(role: ADMIN)`) অথবা রিসলভার মিডলওয়্যারে রিকোয়েস্ট কন্টেক্সটের ইউজার রোল যাচাই করে সংবেদনশীল ফিল্ডে এক্সেস ব্লক করা।
15. **REST to GraphQL:** প্রতিটি রিসলভারে ডেটালোডার ক্যাশ রাখা, সমান্তরাল ব্যাকএন্ড কলগুলোকে `Promise.all()` দিয়ে প্যারালাল করা এবং ওপেনটেলিমেট্রি দিয়ে রিসলভার ট্রেস টাইমলাইন পর্যবেক্ষণ করা।

</details>
