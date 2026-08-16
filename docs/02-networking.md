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

[← Fundamentals](01-system-design-fundamentals.md) · [Roadmap](../ROADMAP.md) · [Databases →](03-databases.md)
