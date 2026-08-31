# CORS (Cross-Origin Resource Sharing)

[Back to Networking topics](README.md) · [Module guide](../02-networking.md)

## 📌 Learning Checklist
- [ ] Understand the Same-Origin Policy (SOP) as the bedrock of web browser security.
- [ ] Master what constitutes an "Origin": Protocol + Domain + Port.
- [ ] Learn how CORS relaxes SOP safely using HTTP headers.
- [ ] Differentiate Simple Requests vs Preflight Requests (`OPTIONS` method).
- [ ] Master core CORS Headers: `Access-Control-Allow-Origin`, `Allow-Methods`, `Allow-Headers`, `Allow-Credentials`, `Max-Age`.
- [ ] Grasp what CORS protects—and the critical truth: **CORS does NOT protect your backend from Postman/curl!**

---

## 📖 Deep Dive Notes

### 1. সহজ সংজ্ঞা ও Intuitive Mental Model

**Same-Origin Policy (SOP)** হলো ওয়েব ব্রাউজারের সবচেয়ে মৌলিক নিরাপত্তা প্রাচীর। এটি একটি অরিজিন থেকে লোড হওয়া স্ক্রিপ্টকে সম্পূর্ণ ভিন্ন কোনো অরিজিনের ডেটা (যেমন: কুকি, ডম, বা এপিআই রেসপন্স) চুরি বা রিড করা থেকে কঠোরভাবে নিষিদ্ধ করে।

আর **CORS (Cross-Origin Resource Sharing)** হলো এমন একটি ডব্লিউথ্রিসি (W3C) স্ট্যান্ডার্ড মেকানিজম যার মাধ্যমে একটি সার্ভার ব্রাউজারকে নির্দিষ্ট কিছু বিশ্বস্ত এক্সটার্নাল অরিজিনকে তার সীমাবদ্ধ ডেটা বা এপিআই পড়ার স্পষ্ট অনুমতি (Relaxation) দেওয়ার সুযোগ দেয়।

```
What is an Origin?
Protocol   Domain / Host        Port
http://    api.example.com      :80
https://   example.com          :443
https://   example.com          :8080

SOP Rule: Protocol, Domain, এবং Port—এই ৩টির যেকোনো একটি ভিন্ন হলেই সেটি "Cross-Origin"!
https://app.com  ──►  https://api.com (Cross-Origin!)
http://app.com   ──►  https://app.com (Cross-Origin - Protocol Mismatch!)
https://app.com  ──►  https://app.com:8443 (Cross-Origin - Port Mismatch!)
```

> 🧠 **Intuitive Mental Model (অ্যাপার্টমেন্টের সতর্ক দারোয়ান অ্যানালজি):**
> আপনি একটি অ্যাপার্টমেন্টে থাকেন (**User Browser**)।
> আপনার ঘরে একটি গোপন ব্যাংকিং ডায়রি আছে (**Bank Data & Cookies**)। 
> পাশের ফ্ল্যাটের এক অচেনা দুষ্টু প্রতিবেশী (**Malicious Website: evil.com**) এসে আপনার ঘরে হাত দিয়ে আপনার ডায়েরির পাতা পড়তে চাইল (**Cross-Origin read**)। 
> দরজায় থাকা বিশ্বস্ত সিকিউরিটি গার্ড (**Browser's SOP**) তাকে লাঠি উঁচিয়ে তাড়িয়ে দিল: "থামো! তোমরা একই পরিবারের নও, ডায়রি পড়তে দেব না!"
> কিন্তু যদি আপনার ব্যাংক নিজেই চিঠি পাঠিয়ে দারোয়ানকে নির্দেশ দেয়: "অমুক অ্যাপকে আমার ব্যালেন্স দেখতে দাও" (**CORS Header: Access-Control-Allow-Origin**), কেবল তখনই দারোয়ান তাকে পড়তে দেয়।

---

### 2. The Golden Rule: "CORS is a Browser-Enforced Security Mechanism!"

সফটওয়্যার ইঞ্জিনিয়ারদের সবচেয়ে সাধারণ ও মারাত্মক ভুল ধারণা:
> ❌ *"আমি ব্যাকএন্ডে CORS বন্ধ করে রেখেছি, তাই হ্যাকার আমার সার্ভারে কোনো রিকোয়েস্ট পাঠাতে পারবে না!"*

- **বাস্তব সত্য:** **CORS কোনো সার্ভার-সাইড সিকিউরিটি ফায়ারওয়াল নয়!**
- কোনো হ্যাকার যদি টার্মিনালে বসে `curl`, Python স্ক্রিপ্ট, বা Postman দিয়ে আপনার ব্যাকএন্ড এপিআই-তে কল করে—**কোনো CORS কাজ করবে না!**
- সার্ভার সানন্দে রিকোয়েস্ট এক্সিকিউট করবে এবং ডেটা ফেরত দেবে।
- **CORS শুধুমাত্র এবং শুধুমাত্র ব্রাউজার দ্বারা পরিচালিত হয়**, যাতে সাধারণ নিরপরাধ ব্যবহারকারী কোনো ভুয়া ওয়েবসাইটে ভিজিট করলে ব্রাউজারের ভেতর থেকে গোপনে অন্য ব্যাংকের ডেটা চুরি না হতে পারে।

---

### 3. Simple Requests vs Preflight Requests

```
Simple Request (GET/POST with standard headers):
Browser ──────────────── GET /api/data ────────────────► Server
Browser ◄─── 200 OK + Access-Control-Allow-Origin ────── Server

Preflight Request (PUT/DELETE/JSON or Custom Headers):
Browser ─── (1) Preflight: OPTIONS /api/data ──────────► Server
            Origin: https://app.com
            Access-Control-Request-Method: DELETE
            Access-Control-Request-Headers: Authorization

Browser ◄── (2) 204 No Content ──────────────────────── Server
            Access-Control-Allow-Origin: https://app.com
            Access-Control-Allow-Methods: GET, DELETE
            Access-Control-Allow-Headers: Authorization
            Access-Control-Max-Age: 86400

Browser ─── (3) Actual Request: DELETE /api/data ──────► Server
Browser ◄── (4) 200 OK (Deletion Successful) ────────── Server
```

#### কখন Preflight (OPTIONS) কল ট্রিগার হয়?
যদি রিকোয়েস্টটি নিচের যেকোনো একটি শর্ত ভঙ্গ করে:
1. মেথডটি `GET`, `HEAD`, বা `POST` ছাড়া অন্য কিছু হয় (যেমন: `PUT`, `DELETE`, `PATCH`)।
2. কন্টেন্ট-টাইপ সাধারণ ফর্ম ডেটা (`text/plain`, `multipart/form-data`, `application/x-www-form-urlencoded`) ছাড়া অন্য কিছু হয় (যেমন: **`application/json`**)।
3. কাস্টম কোনো হেডার পাঠানো হয় (যেমন: **`Authorization: Bearer ...`**, `X-Api-Key`)।

---

### 4. Key CORS Response Headers

1. **`Access-Control-Allow-Origin`:** কোন কোন অরিজিন রেসপন্সটি পড়তে পারবে (যেমন: `https://frontend.com` অথবা উন্মুক্ত পাবলিক এপিআই-এর জন্য `*`)।
2. **`Access-Control-Allow-Methods`:** কোন কোন HTTP মেথড অনুমোদিত (`GET, POST, PUT, DELETE, OPTIONS`)।
3. **`Access-Control-Allow-Headers`:** ক্লায়েন্ট কোন কোন কাস্টম হেডার পাঠাতে পারবে (`Content-Type, Authorization, X-Requested-With`)।
4. **`Access-Control-Allow-Credentials`:** কুকি এবং অথেনটিকেশন ক্রেডেনশিয়াল অনুমোদিত কিনা (`true`)।
   > ⚠️ **গুরুত্বপূর্ণ নিয়ম:** যদি `Access-Control-Allow-Credentials: true` হয়, তবে `Access-Control-Allow-Origin`-এ কখনোই ওয়াইল্ডকার্ড `*` ব্যবহার করা যাবে না! নির্দিষ্ট অরিজিনের নাম হুবহু উল্লেখ থাকতে হবে।
5. **`Access-Control-Max-Age`:** ব্রাউজার কত সেকেন্ড প্রি-ফ্লাইট রেসপন্স ক্যাশ রাখবে (যেমন: `86400` = ২৪ ঘণ্টা), যাতে প্রতি ক্লিকে অতিরিক্ত OPTIONS রিকোয়েস্ট না যায়।

---

### 5. Failure Modes & Production Security Hazards

#### Failure Mode 1: Reflecting the Origin Header blindly with Credentials
- **ঝুঁকি:** অলস ডেভেলপার ওয়াইল্ডকার্ড এরর এড়াতে সার্ভার কোডে লিখে রাখে: 
  `Access-Control-Allow-Origin: req.headers['origin']` এবং সাথে দেয় `Allow-Credentials: true`।
- **বিপর্যয়:** যেকোনো হ্যাকার তার ক্ষতিকর সাইট (`evil.com`) থেকে রিকোয়েস্ট পাঠালে সার্ভার সেই `evil.com`-কেই অনুমোদিত অরিজিন বানিয়ে দেয়। হ্যাকারের সাইট ইউজারের সমস্ত কুকি ও সংবেদনশীল ডেটা নিমেষেই চুরি করে নেয়!
- **প্রতিরোধ (Mitigation):** কঠোরভাবে একটি অনুমোদিত ডোমেনের হোয়াইটলিস্ট (`allowed_origins = ['https://app.com', 'https://admin.com']`) যাচাই করা।

---

### 6. Senior / Staff Engineer Interview Defense

> **ইন্টারভিউয়ার:** *"Preflight OPTIONS রিকোয়েস্ট এপিআই ল্যাটেন্সি দ্বিগুণ করে ফেলে। হাই-থ্রুপুট মোবাইল ও ওয়েব অ্যাপ্লিকেশনে প্রিফ্লাইট ওভারহেড আপনি কীভাবে অপ্টিমাইজ করবেন?"*
>
> 💡 **Staff-Level উত্তরের কাঠামো:**
> "প্রিফ্লাইট রিকোয়েস্ট প্রতিটি এপিআই কলের আগে একটি অতিরিক্ত নেটওয়ার্ক রাউন্ড-ট্রিপ (RTT) যোগ করে, যা ব্যবহারকারীর অভিজ্ঞতায় ১০০-২০০ms ল্যাটেন্সি তৈরি করে। এটি অপ্টিমাইজ করতে ৩টি কৌশল প্রয়োগ করব:
> 1. **Access-Control-Max-Age ক্যাশিং:** সার্ভার প্রিফ্লাইট রেসপন্সে পাঠাবে `Access-Control-Max-Age: 86400` (২৪ ঘণ্টা)। আধুনিক ব্রাউজারগুলো (যেমন Chrome) তাদের নিজস্ব অভ্যন্তরীণ ক্যাপ (ক্রোমে সর্বোচ্চ ২ ঘণ্টা বা ৭২০০ সেকেন্ড) অনুযায়ী প্রিফ্লাইট ক্যাশ রাখবে, ফলে পরবর্তী সমস্ত কলার প্রিফ্লাইট ছাড়াই সরাসরি ডেটা পাঠাবে।
> 2. **এজ রিভার্স প্রক্সিতে হ্যান্ডলিং (Nginx / Cloudflare):** প্রিফ্লাইট রিকোয়েস্ট যেন কখনো ব্যাকএন্ড অ্যাপ্লিকেশন কোড বা নোডজেস প্রসেস পর্যন্ত না যায়। এজ রিভার্স প্রক্সিতে `if ($request_method = 'OPTIONS')` লিখে এজ থেকেই তাৎক্ষণিকভাবে `204 No Content` সহ CORS হেডার রিটার্ন করা হবে (সাব-১ms ল্যাটেন্সি)।
> 3. **Same-Origin BFF Gateway (The Staff Choice):** ফ্রন্টএন্ড এবং ব্যাকএন্ড এপিআই-কে আলাদা ডোমেনে না রেখে (`app.com` এবং `api.com`), একটি রিভার্স প্রক্সি বা এপিআই গেটওয়ের মাধ্যমে একই ডোমেনের অধীনে নিয়ে আসা (`app.com` এবং `app.com/api/*`)। যেহেতু দুটিই হুবহু একই অরিজিন, ব্রাউজার কোনোদিন কোনো প্রিফ্লাইট চালাবে না এবং কোনো CORS-এর প্রয়োজনই হবে না!"

---

## 📝 Practice Questions

```markdown
### Basic Practice Questions
1. Same-Origin Policy (SOP) কী এবং এটি কোন ৩টি উপাদানের ওপর নির্ভর করে?
2. CORS-এর মূল কাজ কী এবং এটি কীসের জন্য ব্যবহৃত হয়?
3. কেন curl বা Postman দিয়ে রিকোয়েস্ট পাঠালে কোনো CORS এরর আসে না?
4. Preflight Request কী এবং এটি কোন HTTP মেথড ব্যবহার করে?
5. `Access-Control-Allow-Origin: *` থাকা অবস্থায় কেন `Access-Control-Allow-Credentials: true` কাজ করে না?

### Intermediate Practice Questions
6. কোন কোন শর্তে একটি রিকোয়েস্টকে "Simple Request" বলা হয় যার জন্য কোনো প্রিফ্লাইটের প্রয়োজন হয় না?
7. `Access-Control-Max-Age` হেডারের কাজ কী এবং এটি কীভাবে নেটওয়ার্ক ল্যাটেন্সি অপ্টিমাইজ করে?
8. ব্রাউজারে একটি রিকোয়েস্ট সফলভাবে HTTP 200 রেসপন্স পাওয়া সত্ত্বেও কনসোলে কেন CORS এরর দেখা দিতে পারে?
9. ক্ষতিকর অরিজিন ব্লাইন্ডলি রিফ্লেক্ট করার নিরাপত্তা ঝুঁকি কী এবং এটি কীভাবে CSRF/ডেটা চুরিতে রূপান্তরিত হয়?
10. `Access-Control-Expose-Headers` কী এবং এটি ক্লায়েন্ট জাভাস্ক্রিপ্টকে কোন সুবিধা দেয়?

### Advanced / Staff-Level Questions
11. Same-Origin Reverse Proxy আর্কিটেকচার (BFF) কীভাবে ক্রস-অরিজিন ওভারহেড ও প্রিফ্লাইট রিকোয়েস্ট সম্পূর্ণ নির্মূল করে?
12. সিডিএন (Cloudflare/CloudFront) ক্যাশিংয়ে `Vary: Origin` হেডার পাঠানো কেন বাধ্যতামূলক এবং এটি না থাকলে কীভাবে এক ইউজারের CORS হেডার অন্য ইউজারের কাছে ক্যাশ পয়জন হয়?
13. মাইক্রোসার্ভিস ব্যাকএন্ডে API Gateway লেভেলে সেন্ট্রালাইজড CORS হ্যান্ডলিং বনাম ইনডিভিজুয়াল মাইক্রোসার্ভিসে CORS হ্যান্ডলিংয়ের আর্কিটেকচারাল ট্রেড-অফ কী?
14. Private Network Access (PNA / CORS-RFC draft): পাবলিক ইন্টারনেট থেকে লোকালহোস্টে (`http://localhost:3000`) এপিআই রিকোয়েস্ট পাঠানো রোধে আধুনিক ব্রাউজারগুলো কীভাবে প্রটেকশন দিচ্ছে?
15. ওয়েব ফন্ট (WOFF2) এবং ক্যানভাসে ড্র করা ক্রস-অরিজিন ইমেজ (Tainted Canvas) কীভাবে CORS পলিসির কারণে ব্রাউজারে আটকে যায় এবং এর সমাধান কী?
```

---

## 🔑 Answer Key & Self-Test Evaluation

<details>
<summary>👉 <b>Answer Key ও সমাধান দেখতে এখানে ক্লিক করুন</b></summary>

1. **SOP ও ৩ উপাদান:** ব্রাউজারের নিরাপত্তা নীতি; উপাদান ৩টি: Protocol, Domain (Host), এবং Port।
2. **CORS কাজ:** সার্ভারের সম্মতিতে ব্রাউজারের কঠোর SOP নীতি শিথিল করে ভিন্ন অরিজিনকে এপিআই ডেটা পড়ার অনুমতি দেওয়া।
3. **curl-এ নো CORS:** CORS শুধুমাত্র ওয়েব ব্রাউজারের একটি ক্লায়েন্ট-সাইড মেকানিজম; ব্রাউজারের বাইরে কোনো curl বা Postman এই নিয়ম মানে না।
4. **Preflight:** আসল রিকোয়েস্টের আগে সার্ভার পারমিশন যাচাই করতে ব্রাউজার কর্তৃক প্রেরিত একটি স্বয়ংক্রিয় `OPTIONS` রিকোয়েস্ট।
5. **Credentials with Wildcard নিষিদ্ধ:** নিরাপত্তার স্বার্থে; ব্রাউজার চায় না কোনো পাবলিক উন্মুক্ত ওয়াইল্ডকার্ড সাইট ব্যবহারকারীর গোপন সেশন কুকি চুরি করার এক্সেস পাক।
6. **Simple Request:** GET/POST/HEAD মেথড, স্ট্যান্ডার্ড হেডার এবং `application/json` ছাড়া সাধারণ ফর্ম বা প্লেইন টেক্সট কন্টেন্ট।
7. **Max-Age:** প্রিফ্লাইট অনুমোদনের মেয়াদ সেকেন্ডে ক্যাশ রাখা, ফলে প্রতি ক্লিকে অতিরিক্ত OPTIONS কল করতে হয় না।
8. **200 বাট CORS Error:** সার্ভার সফলভাবে ডেটা জেনারেট করে পাঠিয়েছে, কিন্তু রেসপন্সে ব্রাউজারের প্রত্যাশিত CORS হেডার ছিল না, তাই ব্রাউজার নিজের হাতে ডেটা ব্লক করেছে।
9. **Origin Reflection ঝুঁকি:** যেকেউ তার সাইট থেকে কল করলেই সার্ভার তাকে অনুমতি দিয়ে দেয়, ফলে আক্রমণকারী ইউজারের গোপন কুকি দিয়ে সংরক্ষিত ডেটা পড়তে পারে।
10. **Expose-Headers:** ডিফল্টভাবে জাভাস্ক্রিপ্ট শুধু বেসিক হেডার দেখতে পায়; কাস্টম হেডার (যেমন `X-Total-Count`) ব্রাউজারে পড়তে এটি লাগে।
11. **BFF Same-Origin:** ফ্রন্টএন্ড ও ব্যাকএন্ড একই ডোমেনের পাথ (`/api/`) দিয়ে রাউট করা, ফলে ব্রাউজারের চোখে এটি সেম-অরিজিন হওয়ায় কোনো প্রিফ্লাইটই লাগে না।
12. **Vary: Origin:** সিডিএনকে নির্দেশ দেওয়া অরিজিনভেদে আলাদা ক্যাশ কপি রাখতে। না দিলে Site A-র অনুমোদিত CORS হেডার Site B-র ইউজারের কাছে ক্যাশড হয়ে এরর দেবে।
13. **Gateway CORS:** গেটওয়েতে সেন্ট্রাল হ্যান্ডলিং করলে মাইক্রোসার্ভিস কোড ক্লিন থাকে এবং ডাবল-হেডার কনফ্লিক্ট ঘটে না।
14. **PNA Protection:** হ্যাকার সাইট থেকে ইউজারের লোকাল রাউটার বা লোকালহোস্টে কল করা ঠেকাতে ব্রাউজার বিশেষ `Access-Control-Request-Private-Network` প্রিফ্লাইট পাঠায়।
15. **Tainted Canvas:** ক্রস-অরিজিন ছবি আঁকলে ক্যানভাস লক হয়ে যায় (`toDataURL()` কাজ করে না)। সমাধান: ইমেজ ট্যাগে `crossorigin="anonymous"` এবং সার্ভারে সঠিক CORS হেডার রাখা।

</details>
