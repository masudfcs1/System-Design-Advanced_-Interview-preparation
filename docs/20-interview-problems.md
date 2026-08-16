# Module 20 · Real System Design Problems

**Goal:** Turn knowledge into repeatable, timed design performance.  
**Suggested time:** ongoing; complete at least 30 designs

## Practice method

Use the [System Design Worksheet](../templates/system-design-template.md). For each problem:

1. Clarify scope and prioritize three functional requirements.
2. Set measurable scale, latency, availability, consistency, and durability targets.
3. Define APIs, core entities, and access patterns.
4. Draw the high-level read and write paths.
5. Deep-dive into two risks or bottlenecks.
6. Cover reliability, security, observability, and evolution.
7. Record trade-offs and feedback in [PROGRESS.md](../PROGRESS.md).

## বাংলা system-design practice prompt

Problem catalog থেকে একটি system বসিয়ে prompt-টি কপি করুন—যেমন `URL Shortener`, `WhatsApp`, `Netflix`, `Uber`, `Payment Gateway`, `Google Drive`, `Search Engine`, অথবা `Distributed Job Scheduler`।

```text
আমি Senior/Staff Engineer System Design interview-এর জন্য [SYSTEM] design practice করছি। আমাকে সরাসরি final design দেবেন না। Interviewer-এর মতো এক ধাপ করে বাংলায় guide করুন এবং প্রতিটি ধাপে আমার উত্তর দেওয়ার জন্য অপেক্ষা করুন। Technical term English-এ রাখবেন।

Interview flow:
1. প্রথমে ambiguous requirements দিন এবং আমাকে clarifying questions করতে বলুন
2. functional/non-functional requirements ও out-of-scope যাচাই করুন
3. scale estimation (QPS, storage, bandwidth, peak traffic) করতে বলুন
4. API, data model ও access pattern design করতে বলুন
5. high-level architecture আঁকার জন্য Mermaid-compatible text flow চাইুন
6. read path এবং write path deep dive করান
7. database, cache, queue, partitioning ও consistency choices challenge করুন
8. bottleneck, hot key, failure, retry, idempotency ও backpressure scenario দিন
9. security, observability, disaster recovery ও cost নিয়ে follow-up করুন
10. 10×/100× scale বা নতুন requirement দিয়ে design evolve করতে বলুন
11. শেষে আমার উত্তরের strengths, gaps এবং 24-point scorecard দিন
12. আরও practice-এর জন্য 10টি top follow-up interview question আলাদা copyable code block-এ দিন
13. সবশেষে একটি concise reference design এবং key trade-offs দিন

System: [SYSTEM]
Interview level: Senior/Staff
Time limit: 45 minutes
```

## Problem catalog

### Level 1 · Core building blocks

- [ ] URL shortener / TinyURL
- [ ] Notification service
- [ ] Email service
- [ ] Push notification system
- [ ] Shopping cart
- [ ] Inventory management
- [ ] Rate limiter
- [ ] File storage such as Google Drive or Dropbox

### Level 2 · Social and communication

- [ ] Chat application / Messenger / WhatsApp
- [ ] Slack or Discord
- [ ] News feed / Facebook Feed / X / LinkedIn
- [ ] Instagram or Reddit
- [ ] Collaborative document editor
- [ ] Video meeting such as Zoom or Google Meet

### Level 3 · Media, search, and discovery

- [ ] Video streaming such as Netflix or YouTube
- [ ] Music streaming such as Spotify
- [ ] Live-streaming platform
- [ ] Search engine
- [ ] Recommendation engine
- [ ] Real-time multiplayer game backend

### Level 4 · Marketplaces and geospatial systems

- [ ] Ride sharing such as Uber
- [ ] Food delivery / Uber Eats
- [ ] Accommodation marketplace / Airbnb
- [ ] Google Maps-style mapping
- [ ] E-commerce marketplace / Amazon
- [ ] Hotel, flight, or ticket booking

### Level 5 · Financial and high-integrity systems

- [ ] Payment gateway / PayPal / Stripe
- [ ] Banking system
- [ ] ATM network
- [ ] Trading platform
- [ ] Cryptocurrency exchange

### Level 6 · Platform scale

- [ ] IoT device-management platform
- [ ] Multi-channel notification platform
- [ ] Global feature-flag service
- [ ] Distributed job scheduler
- [ ] Metrics ingestion and alerting platform

## Scorecard

Score each category from 0 (missing) to 3 (strong and defended).

| Category | 0–3 |
|---|---:|
| Requirements and scope | |
| Estimation and constraints | |
| API and data model | |
| High-level architecture | |
| Scale and bottlenecks | |
| Reliability and consistency | |
| Security and observability | |
| Trade-offs and communication | |

Target **19/24 or higher twice in a row** before increasing problem difficulty.

## After-study checkpoint

- [ ] Complete 10 guided designs.
- [ ] Complete 10 independent designs.
- [ ] Complete 10 timed designs.
- [ ] Rework the five lowest-scoring designs.
- [ ] Participate in at least two mock interviews.

**Complete when:** you can lead a 45-minute design, adapt to changed requirements, and clearly defend trade-offs.

[← Performance](19-performance-optimization.md) · [Roadmap](../ROADMAP.md) · [Advanced topics →](21-advanced-topics.md)
