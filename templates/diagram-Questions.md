# 🏗️ System Design Interview Questions — Complete Roadmap

> A comprehensive collection of system design interview questions organized by experience level,
> complete with architecture diagrams, key components, and design considerations.

---

## 📋 Table of Contents

- [Overview Diagram](#overview-diagram)
- [Fresher Level System Design (0–3 Years)](#fresher-level-system-design)
- [Experienced Level System Design (3–15+ Years)](#experienced-level-system-design)
- [Quick Reference Matrix](#quick-reference-matrix)

---

## Overview Diagram

```mermaid
mindmap
  root((System Design<br/>Interview Questions))
    Fresher Level
      URL Shortener
      Chat App
      File Storage
      Social Media
      Search Engine
      E-Commerce
      Ride Sharing
      Video Streaming
      Recommendation
      Food Delivery
      Parking Lot
      Music Streaming
      Ticket Booking
      Note Taking
      Weather Forecast
      Email Service
      File Sync
      Calendar App
      Quiz Platform
      Auth System
    Experienced Level
      bit.ly Scale
      Redis Clone
      Facebook Scale
      Netflix Scale
      HDFS Clone
      WhatsApp Scale
      Google Crawler
      Memcached Clone
      CDN like Cloudflare
      Google Search
      Uber Scale
      YouTube Scale
      Zomato Scale
      Google Docs
      Amazon Scale
      Marketplace Recommender
      Distributed DB
      Twitter Scale
      Instagram Scale
      Task Scheduler
```

---

## Fresher Level System Design

> **Target Audience:** 0–3 years of experience
> **Focus:** Core concepts, basic architecture, single-service design, fundamental data modeling

---

### 1. Design a Simple URL Shortening Service

```mermaid
graph TB
    subgraph Client Layer
        U["👤 User"]
    end

    subgraph API Gateway
        AG["API Gateway<br/>Rate Limiting"]
    end

    subgraph Application Layer
        SVC["URL Shortener Service"]
        ENC["Base62 Encoder"]
        VAL["URL Validator"]
    end

    subgraph Data Layer
        DB[("Database<br/>(URL Mappings)")]
        CACHE["Cache<br/>(Hot URLs)"]
    end

    U -->|"POST /shorten"| AG
    U -->|"GET /:shortCode"| AG
    AG --> SVC
    SVC --> ENC
    SVC --> VAL
    SVC --> CACHE
    CACHE -->|"Cache Miss"| DB
    SVC -->|"Write"| DB

    style U fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style AG fill:#F5A623,stroke:#C4841C,color:#fff
    style SVC fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style ENC fill:#50C878,stroke:#3A9A5C,color:#fff
    style VAL fill:#50C878,stroke:#3A9A5C,color:#fff
    style DB fill:#E74C3C,stroke:#C0392B,color:#fff
    style CACHE fill:#E67E22,stroke:#D35400,color:#fff
```

**Key Discussion Points:**
- Hash function vs counter-based ID generation
- Read-heavy workload optimization
- Cache eviction policies (LRU)
- 301 vs 302 redirects
- Analytics and click tracking

---

### 2. Design a Basic Chat Application

```mermaid
graph TB
    subgraph Clients
        U1["👤 User A"]
        U2["👤 User B"]
    end

    subgraph Connection Layer
        WS["WebSocket Server"]
        LB["Load Balancer"]
    end

    subgraph Application Layer
        MS["Message Service"]
        PS["Presence Service"]
        NS["Notification Service"]
    end

    subgraph Data Layer
        MDB[("Message Store")]
        UDB[("User Store")]
        MQ["Message Queue"]
    end

    U1 -->|"WebSocket"| LB
    U2 -->|"WebSocket"| LB
    LB --> WS
    WS --> MS
    WS --> PS
    MS --> MDB
    MS --> MQ
    MQ --> NS
    PS --> UDB

    style U1 fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style U2 fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style WS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style LB fill:#F5A623,stroke:#C4841C,color:#fff
    style MS fill:#50C878,stroke:#3A9A5C,color:#fff
    style PS fill:#50C878,stroke:#3A9A5C,color:#fff
    style NS fill:#50C878,stroke:#3A9A5C,color:#fff
    style MDB fill:#E74C3C,stroke:#C0392B,color:#fff
    style UDB fill:#E74C3C,stroke:#C0392B,color:#fff
    style MQ fill:#E67E22,stroke:#D35400,color:#fff
```

**Key Discussion Points:**
- WebSocket vs Long Polling vs SSE
- Message ordering and delivery guarantees
- Online/offline status tracking
- Group chat vs 1:1 messaging
- Message persistence and history

---

### 3. Design a File Storage System

```mermaid
graph TB
    subgraph Client
        U["👤 User"]
        APP["Desktop / Mobile App"]
    end

    subgraph API Layer
        GW["API Gateway"]
        AUTH["Auth Service"]
    end

    subgraph Core Services
        FS["File Service"]
        META["Metadata Service"]
        CHUNK["Chunking Service"]
    end

    subgraph Storage
        OBJ["Object Storage<br/>(File Chunks)"]
        MDB[("Metadata DB")]
    end

    U --> APP
    APP --> GW
    GW --> AUTH
    GW --> FS
    FS --> META
    FS --> CHUNK
    CHUNK --> OBJ
    META --> MDB

    style U fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style APP fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style GW fill:#F5A623,stroke:#C4841C,color:#fff
    style AUTH fill:#F5A623,stroke:#C4841C,color:#fff
    style FS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style META fill:#50C878,stroke:#3A9A5C,color:#fff
    style CHUNK fill:#50C878,stroke:#3A9A5C,color:#fff
    style OBJ fill:#E74C3C,stroke:#C0392B,color:#fff
    style MDB fill:#E74C3C,stroke:#C0392B,color:#fff
```

**Key Discussion Points:**
- File chunking for large files
- Deduplication strategies
- Access control and permissions
- Versioning and conflict resolution
- Upload resumability

---

### 4. Design a Simple Social Media Platform

```mermaid
graph TB
    subgraph Users
        U["👤 User"]
    end

    subgraph Gateway
        LB["Load Balancer"]
        API["API Server"]
    end

    subgraph Services
        US["User Service"]
        PS["Post Service"]
        FD["Feed Service"]
        NS["Notification Service"]
        LS["Like/Comment Service"]
    end

    subgraph Data
        UDB[("User DB")]
        PDB[("Post DB")]
        GDB[("Graph DB<br/>Followers")]
        CACHE["Feed Cache"]
        BLOB["Media Storage"]
    end

    U --> LB --> API
    API --> US --> UDB
    API --> PS --> PDB
    API --> FD --> CACHE
    API --> LS --> PDB
    PS --> BLOB
    FD --> GDB
    PS --> NS

    style U fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style LB fill:#F5A623,stroke:#C4841C,color:#fff
    style API fill:#F5A623,stroke:#C4841C,color:#fff
    style US fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style PS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style FD fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style NS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style LS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style UDB fill:#E74C3C,stroke:#C0392B,color:#fff
    style PDB fill:#E74C3C,stroke:#C0392B,color:#fff
    style GDB fill:#E74C3C,stroke:#C0392B,color:#fff
    style CACHE fill:#E67E22,stroke:#D35400,color:#fff
    style BLOB fill:#E67E22,stroke:#D35400,color:#fff
```

**Key Discussion Points:**
- Fan-out on write vs fan-out on read
- News feed generation algorithm
- Follower/following graph storage
- Media upload and CDN
- Privacy and content moderation

---

### 5. Design a Simple Search Engine

```mermaid
graph TB
    subgraph User
        U["👤 User"]
    end

    subgraph Query Processing
        QP["Query Parser"]
        SP["Spell Checker"]
        TK["Tokenizer"]
    end

    subgraph Search Core
        IDX["Inverted Index"]
        RK["Ranking Engine<br/>(TF-IDF)"]
        CACHE["Result Cache"]
    end

    subgraph Indexing Pipeline
        CR["Web Crawler"]
        PR["HTML Parser"]
        IB["Index Builder"]
    end

    subgraph Storage
        DS[("Document Store")]
        IS[("Index Store")]
    end

    U -->|"Search Query"| QP
    QP --> SP --> TK
    TK --> IDX
    IDX --> RK
    RK --> CACHE
    CR --> PR --> IB
    IB --> IS
    IB --> DS
    IDX --> IS

    style U fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style QP fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style SP fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style TK fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style IDX fill:#50C878,stroke:#3A9A5C,color:#fff
    style RK fill:#50C878,stroke:#3A9A5C,color:#fff
    style CACHE fill:#E67E22,stroke:#D35400,color:#fff
    style CR fill:#F5A623,stroke:#C4841C,color:#fff
    style PR fill:#F5A623,stroke:#C4841C,color:#fff
    style IB fill:#F5A623,stroke:#C4841C,color:#fff
    style DS fill:#E74C3C,stroke:#C0392B,color:#fff
    style IS fill:#E74C3C,stroke:#C0392B,color:#fff
```

**Key Discussion Points:**
- Inverted index data structure
- TF-IDF ranking algorithm
- Crawl scheduling and politeness
- Query auto-complete
- Spell correction and synonyms

---

### 6. Design a Simple E-Commerce Website

```mermaid
graph TB
    subgraph Client
        U["👤 Buyer"]
    end

    subgraph Gateway
        LB["Load Balancer"]
        API["API Gateway"]
    end

    subgraph Services
        PS["Product Service"]
        CS["Cart Service"]
        OS["Order Service"]
        PAY["Payment Service"]
        INV["Inventory Service"]
    end

    subgraph Data
        PDB[("Product DB")]
        ODB[("Order DB")]
        CACHE["Product Cache"]
        MQ["Order Queue"]
    end

    U --> LB --> API
    API --> PS --> PDB
    API --> CS --> CACHE
    API --> OS --> ODB
    OS --> PAY
    OS --> INV
    OS --> MQ

    style U fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style LB fill:#F5A623,stroke:#C4841C,color:#fff
    style API fill:#F5A623,stroke:#C4841C,color:#fff
    style PS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style CS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style OS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style PAY fill:#50C878,stroke:#3A9A5C,color:#fff
    style INV fill:#50C878,stroke:#3A9A5C,color:#fff
    style PDB fill:#E74C3C,stroke:#C0392B,color:#fff
    style ODB fill:#E74C3C,stroke:#C0392B,color:#fff
    style CACHE fill:#E67E22,stroke:#D35400,color:#fff
    style MQ fill:#E67E22,stroke:#D35400,color:#fff
```

**Key Discussion Points:**
- Product catalog and search
- Shopping cart (session vs persistent)
- Payment processing flow
- Inventory management and race conditions
- Order state machine

---

### 7. Design a Basic Ride-Sharing System

```mermaid
graph TB
    subgraph Users
        R["👤 Rider"]
        D["🚗 Driver"]
    end

    subgraph Gateway
        API["API Gateway"]
    end

    subgraph Core Services
        MS["Matching Service"]
        LS["Location Service"]
        TS["Trip Service"]
        PS["Pricing Service"]
        PAY["Payment Service"]
    end

    subgraph Data
        GEO["Geospatial Index<br/>(QuadTree)"]
        TDB[("Trip DB")]
        MQ["Event Queue"]
    end

    R -->|"Request Ride"| API
    D -->|"Update Location"| API
    API --> MS
    API --> LS
    MS --> GEO
    LS --> GEO
    MS --> PS
    MS --> TS --> TDB
    TS --> PAY
    TS --> MQ

    style R fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style D fill:#50C878,stroke:#3A9A5C,color:#fff
    style API fill:#F5A623,stroke:#C4841C,color:#fff
    style MS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style LS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style TS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style PS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style PAY fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style GEO fill:#E74C3C,stroke:#C0392B,color:#fff
    style TDB fill:#E74C3C,stroke:#C0392B,color:#fff
    style MQ fill:#E67E22,stroke:#D35400,color:#fff
```

**Key Discussion Points:**
- Geospatial indexing (QuadTree, Geohash)
- Real-time driver location tracking
- Matching algorithm (nearest driver)
- Surge pricing model
- ETA calculation

---

### 8. Design a Basic Video Streaming Service

```mermaid
graph TB
    subgraph Users
        U["👤 Viewer"]
        C["🎬 Creator"]
    end

    subgraph Delivery
        CDN["CDN<br/>(Edge Servers)"]
        LB["Load Balancer"]
    end

    subgraph Services
        VS["Video Service"]
        TP["Transcoding Pipeline"]
        SS["Search Service"]
        RS["Recommendation Service"]
    end

    subgraph Storage
        RAW["Raw Video Storage"]
        ENC["Encoded Videos<br/>(Multiple Resolutions)"]
        MDB[("Metadata DB")]
    end

    C -->|"Upload"| LB --> VS
    VS --> RAW
    RAW --> TP --> ENC
    VS --> MDB
    U -->|"Watch"| CDN
    CDN --> ENC
    U -->|"Search"| SS --> MDB
    U -->|"Browse"| RS --> MDB

    style U fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style C fill:#50C878,stroke:#3A9A5C,color:#fff
    style CDN fill:#F5A623,stroke:#C4841C,color:#fff
    style LB fill:#F5A623,stroke:#C4841C,color:#fff
    style VS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style TP fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style SS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style RS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style RAW fill:#E74C3C,stroke:#C0392B,color:#fff
    style ENC fill:#E74C3C,stroke:#C0392B,color:#fff
    style MDB fill:#E74C3C,stroke:#C0392B,color:#fff
```

**Key Discussion Points:**
- Video transcoding (multiple resolutions)
- Adaptive bitrate streaming (HLS/DASH)
- CDN for low-latency delivery
- Thumbnail generation
- Video chunking and seek support

---

### 9. Design a Simple Recommendation System

```mermaid
graph TB
    subgraph User
        U["👤 User"]
    end

    subgraph API
        API["API Server"]
    end

    subgraph Recommendation Engine
        CF["Collaborative Filtering"]
        CB["Content-Based Filtering"]
        HY["Hybrid Ranker"]
    end

    subgraph Data Pipeline
        EL["Event Logger"]
        FE["Feature Extractor"]
        MT["Model Trainer<br/>(Batch)"]
    end

    subgraph Storage
        UH[("User History")]
        IP[("Item Profiles")]
        MODEL["Trained Model"]
        CACHE["Result Cache"]
    end

    U --> API
    API --> HY
    HY --> CF --> UH
    HY --> CB --> IP
    HY --> CACHE
    U -->|"Actions"| EL --> FE
    FE --> MT --> MODEL
    CF --> MODEL

    style U fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style API fill:#F5A623,stroke:#C4841C,color:#fff
    style CF fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style CB fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style HY fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style EL fill:#50C878,stroke:#3A9A5C,color:#fff
    style FE fill:#50C878,stroke:#3A9A5C,color:#fff
    style MT fill:#50C878,stroke:#3A9A5C,color:#fff
    style UH fill:#E74C3C,stroke:#C0392B,color:#fff
    style IP fill:#E74C3C,stroke:#C0392B,color:#fff
    style MODEL fill:#E67E22,stroke:#D35400,color:#fff
    style CACHE fill:#E67E22,stroke:#D35400,color:#fff
```

**Key Discussion Points:**
- Collaborative vs content-based filtering
- Cold start problem
- Feature engineering
- A/B testing recommendations
- Real-time vs batch recommendation

---

### 10. Design a Basic Food Delivery App

```mermaid
graph TB
    subgraph Users
        CU["👤 Customer"]
        RE["🍕 Restaurant"]
        DR["🛵 Delivery Agent"]
    end

    subgraph Gateway
        API["API Gateway"]
    end

    subgraph Services
        OS["Order Service"]
        RS["Restaurant Service"]
        DS["Delivery Service"]
        LS["Location Service"]
        PAY["Payment Service"]
        NS["Notification Service"]
    end

    subgraph Data
        ODB[("Order DB")]
        RDB[("Restaurant DB")]
        GEO["Geospatial Index"]
        MQ["Event Queue"]
    end

    CU --> API
    RE --> API
    DR --> API
    API --> OS --> ODB
    API --> RS --> RDB
    API --> DS --> GEO
    API --> LS --> GEO
    OS --> PAY
    OS --> MQ --> NS

    style CU fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style RE fill:#50C878,stroke:#3A9A5C,color:#fff
    style DR fill:#E67E22,stroke:#D35400,color:#fff
    style API fill:#F5A623,stroke:#C4841C,color:#fff
    style OS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style RS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style DS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style LS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style PAY fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style NS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style ODB fill:#E74C3C,stroke:#C0392B,color:#fff
    style RDB fill:#E74C3C,stroke:#C0392B,color:#fff
    style GEO fill:#E74C3C,stroke:#C0392B,color:#fff
    style MQ fill:#E67E22,stroke:#D35400,color:#fff
```

**Key Discussion Points:**
- Three-sided marketplace design
- Real-time order tracking
- Restaurant search and menu management
- Delivery agent assignment algorithm
- Order state machine

---

### 11. Design a Parking Lot Management System

```mermaid
graph TB
    subgraph Entry/Exit
        EN["🚧 Entry Gate"]
        EX["🚧 Exit Gate"]
    end

    subgraph Core System
        PM["Parking Manager"]
        SA["Spot Allocator"]
        TK["Ticket Service"]
        PAY["Payment Service"]
    end

    subgraph Data
        DB[("Parking DB")]
        DIS["Display Board"]
    end

    EN -->|"Vehicle Arrives"| PM
    PM --> SA -->|"Find Spot"| DB
    SA --> TK
    TK -->|"Issue Ticket"| EN
    EX -->|"Vehicle Leaves"| PM
    PM --> PAY
    PAY -->|"Calculate Fee"| DB
    SA --> DIS

    style EN fill:#F5A623,stroke:#C4841C,color:#fff
    style EX fill:#F5A623,stroke:#C4841C,color:#fff
    style PM fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style SA fill:#50C878,stroke:#3A9A5C,color:#fff
    style TK fill:#50C878,stroke:#3A9A5C,color:#fff
    style PAY fill:#50C878,stroke:#3A9A5C,color:#fff
    style DB fill:#E74C3C,stroke:#C0392B,color:#fff
    style DIS fill:#4A90D9,stroke:#2C5F8A,color:#fff
```

**Key Discussion Points:**
- OOP design (Vehicle, Spot, Ticket classes)
- Spot allocation strategy (nearest, type-based)
- Pricing model (hourly, daily, monthly)
- Concurrency for spot allocation
- Multi-floor and multi-entrance support

---

### 12. Design a Simple Music Streaming Service

```mermaid
graph TB
    subgraph User
        U["👤 Listener"]
    end

    subgraph Delivery
        CDN["CDN<br/>(Audio Streaming)"]
    end

    subgraph Services
        PS["Playback Service"]
        SS["Search Service"]
        PL["Playlist Service"]
        RS["Recommendation"]
        LIB["Library Service"]
    end

    subgraph Data
        ADB[("Audio Storage")]
        MDB[("Metadata DB")]
        UDB[("User Preferences")]
        CACHE["Playlist Cache"]
    end

    U --> PS --> CDN --> ADB
    U --> SS --> MDB
    U --> PL --> CACHE
    PL --> MDB
    U --> LIB --> UDB
    RS --> UDB
    RS --> MDB

    style U fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style CDN fill:#F5A623,stroke:#C4841C,color:#fff
    style PS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style SS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style PL fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style RS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style LIB fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style ADB fill:#E74C3C,stroke:#C0392B,color:#fff
    style MDB fill:#E74C3C,stroke:#C0392B,color:#fff
    style UDB fill:#E74C3C,stroke:#C0392B,color:#fff
    style CACHE fill:#E67E22,stroke:#D35400,color:#fff
```

**Key Discussion Points:**
- Audio encoding formats (MP3, AAC, FLAC)
- Streaming protocols (HTTP progressive, HLS)
- Playlist CRUD operations
- Offline download support
- Royalty tracking per play

---

### 13. Design a Basic Online Ticket Booking System

```mermaid
graph TB
    subgraph User
        U["👤 User"]
    end

    subgraph Gateway
        API["API Gateway"]
    end

    subgraph Services
        ES["Event Service"]
        BS["Booking Service"]
        SS["Seat Selection"]
        PAY["Payment Service"]
        NS["Notification"]
    end

    subgraph Data
        EDB[("Event DB")]
        BDB[("Booking DB")]
        LOCK["Distributed Lock<br/>(Seat Reservation)"]
        MQ["Confirmation Queue"]
    end

    U --> API
    API --> ES --> EDB
    API --> BS --> BDB
    BS --> SS --> LOCK
    BS --> PAY
    PAY -->|"Success"| MQ --> NS

    style U fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style API fill:#F5A623,stroke:#C4841C,color:#fff
    style ES fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style BS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style SS fill:#50C878,stroke:#3A9A5C,color:#fff
    style PAY fill:#50C878,stroke:#3A9A5C,color:#fff
    style NS fill:#50C878,stroke:#3A9A5C,color:#fff
    style EDB fill:#E74C3C,stroke:#C0392B,color:#fff
    style BDB fill:#E74C3C,stroke:#C0392B,color:#fff
    style LOCK fill:#E67E22,stroke:#D35400,color:#fff
    style MQ fill:#E67E22,stroke:#D35400,color:#fff
```

**Key Discussion Points:**
- Seat locking (pessimistic vs optimistic)
- Double booking prevention
- Temporary reservation with TTL
- Payment timeout handling
- Waitlist and cancellation flow

---

### 14. Design a Simple Note-Taking Application

```mermaid
graph TB
    subgraph Clients
        WEB["🌐 Web"]
        MOB["📱 Mobile"]
    end

    subgraph API
        GW["API Gateway"]
        AUTH["Auth Service"]
    end

    subgraph Services
        NS["Note Service"]
        SY["Sync Service"]
        SR["Search Service"]
        SH["Share Service"]
    end

    subgraph Data
        NDB[("Note DB")]
        IDX["Search Index"]
        BLOB["Attachment Storage"]
    end

    WEB --> GW
    MOB --> GW
    GW --> AUTH
    GW --> NS --> NDB
    GW --> SY --> NDB
    GW --> SR --> IDX
    GW --> SH
    NS --> BLOB

    style WEB fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style MOB fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style GW fill:#F5A623,stroke:#C4841C,color:#fff
    style AUTH fill:#F5A623,stroke:#C4841C,color:#fff
    style NS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style SY fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style SR fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style SH fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style NDB fill:#E74C3C,stroke:#C0392B,color:#fff
    style IDX fill:#E74C3C,stroke:#C0392B,color:#fff
    style BLOB fill:#E67E22,stroke:#D35400,color:#fff
```

**Key Discussion Points:**
- Rich text / Markdown storage
- Offline-first architecture
- Conflict resolution (CRDT vs OT)
- Tagging and folder organization
- Full-text search indexing

---

### 15. Design a Weather Forecasting System

```mermaid
graph TB
    subgraph Data Sources
        SAT["🛰️ Satellite"]
        SEN["📡 Sensors"]
        WS["Weather Stations"]
    end

    subgraph Ingestion
        DC["Data Collector"]
        CL["Data Cleaner"]
    end

    subgraph Processing
        ML["Forecasting Model"]
        AG["Data Aggregator"]
    end

    subgraph Delivery
        API["API Server"]
        PUSH["Push Notification"]
        CACHE["Forecast Cache"]
    end

    subgraph Consumers
        U["👤 Users"]
        APP["📱 Weather App"]
    end

    SAT --> DC
    SEN --> DC
    WS --> DC
    DC --> CL --> AG
    AG --> ML
    ML --> CACHE
    CACHE --> API
    API --> APP --> U
    ML --> PUSH --> U

    style SAT fill:#50C878,stroke:#3A9A5C,color:#fff
    style SEN fill:#50C878,stroke:#3A9A5C,color:#fff
    style WS fill:#50C878,stroke:#3A9A5C,color:#fff
    style DC fill:#F5A623,stroke:#C4841C,color:#fff
    style CL fill:#F5A623,stroke:#C4841C,color:#fff
    style ML fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style AG fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style API fill:#E67E22,stroke:#D35400,color:#fff
    style PUSH fill:#E67E22,stroke:#D35400,color:#fff
    style CACHE fill:#E67E22,stroke:#D35400,color:#fff
    style U fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style APP fill:#4A90D9,stroke:#2C5F8A,color:#fff
```

**Key Discussion Points:**
- Time-series data ingestion
- Geospatial data modeling
- Forecast accuracy and model selection
- Caching strategies for hot locations
- Severe weather alert system

---

### 16. Design a Basic Email Service

```mermaid
graph TB
    subgraph Sender
        S["👤 Sender"]
    end

    subgraph Send Path
        SMTP["SMTP Server"]
        SQ["Send Queue"]
        DL["Delivery Service"]
    end

    subgraph Receive Path
        MX["MX Record / Receiver"]
        IMAP["IMAP/POP3 Server"]
        SP["Spam Filter"]
    end

    subgraph Storage
        MB[("Mailbox Store")]
        IDX["Search Index"]
        BLOB["Attachment Store"]
    end

    subgraph Recipient
        R["👤 Recipient"]
    end

    S --> SMTP --> SQ --> DL
    DL -->|"DNS MX Lookup"| MX
    MX --> SP --> MB
    MB --> IDX
    MB --> BLOB
    R --> IMAP --> MB

    style S fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style R fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style SMTP fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style SQ fill:#E67E22,stroke:#D35400,color:#fff
    style DL fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style MX fill:#50C878,stroke:#3A9A5C,color:#fff
    style IMAP fill:#50C878,stroke:#3A9A5C,color:#fff
    style SP fill:#F5A623,stroke:#C4841C,color:#fff
    style MB fill:#E74C3C,stroke:#C0392B,color:#fff
    style IDX fill:#E74C3C,stroke:#C0392B,color:#fff
    style BLOB fill:#E74C3C,stroke:#C0392B,color:#fff
```

**Key Discussion Points:**
- SMTP/IMAP/POP3 protocols
- Spam filtering (Bayesian, rule-based)
- Email storage (per-user mailbox)
- Attachment handling and size limits
- DNS MX record resolution

---

### 17. Design a File Synchronization System

```mermaid
graph TB
    subgraph Devices
        D1["💻 Device A"]
        D2["💻 Device B"]
        D3["📱 Device C"]
    end

    subgraph Sync Layer
        SS["Sync Server"]
        CD["Change Detector<br/>(File Watcher)"]
        CR["Conflict Resolver"]
    end

    subgraph Storage
        CS["Cloud Storage<br/>(Chunks)"]
        MDB[("Metadata DB<br/>Version History)"]
        MQ["Change Queue"]
    end

    D1 -->|"File Changes"| CD
    D2 -->|"File Changes"| CD
    D3 -->|"File Changes"| CD
    CD --> SS
    SS --> CR
    SS --> MQ
    MQ -->|"Propagate"| D1
    MQ -->|"Propagate"| D2
    MQ -->|"Propagate"| D3
    SS --> CS
    SS --> MDB

    style D1 fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style D2 fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style D3 fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style SS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style CD fill:#50C878,stroke:#3A9A5C,color:#fff
    style CR fill:#50C878,stroke:#3A9A5C,color:#fff
    style CS fill:#E74C3C,stroke:#C0392B,color:#fff
    style MDB fill:#E74C3C,stroke:#C0392B,color:#fff
    style MQ fill:#E67E22,stroke:#D35400,color:#fff
```

**Key Discussion Points:**
- File diff and delta sync
- Conflict detection and resolution
- Chunked transfer for large files
- Eventual consistency model
- Bandwidth optimization

---

### 18. Design a Simple Calendar Application

```mermaid
graph TB
    subgraph Users
        U1["👤 User A"]
        U2["👤 User B"]
    end

    subgraph API
        API["API Server"]
    end

    subgraph Services
        ES["Event Service"]
        RS["Reminder Service"]
        AV["Availability Service"]
        SH["Sharing Service"]
    end

    subgraph Data
        EDB[("Event DB")]
        NS["Notification Service"]
        CACHE["Availability Cache"]
    end

    U1 --> API
    U2 --> API
    API --> ES --> EDB
    API --> RS --> NS
    API --> AV --> CACHE
    API --> SH
    AV --> EDB

    style U1 fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style U2 fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style API fill:#F5A623,stroke:#C4841C,color:#fff
    style ES fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style RS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style AV fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style SH fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style EDB fill:#E74C3C,stroke:#C0392B,color:#fff
    style NS fill:#50C878,stroke:#3A9A5C,color:#fff
    style CACHE fill:#E67E22,stroke:#D35400,color:#fff
```

**Key Discussion Points:**
- Recurring event representation (RFC 5545)
- Time zone handling
- Free/busy slot computation
- Calendar sharing and ACL
- Reminder scheduling

---

### 19. Design a Basic Online Quiz Platform

```mermaid
graph TB
    subgraph Users
        ST["👤 Student"]
        TC["👨‍🏫 Teacher"]
    end

    subgraph API
        API["API Gateway"]
    end

    subgraph Services
        QS["Quiz Service"]
        SS["Submission Service"]
        GS["Grading Service"]
        LB["Leaderboard"]
    end

    subgraph Data
        QDB[("Quiz DB")]
        SDB[("Submission DB")]
        CACHE["Leaderboard Cache"]
        TM["Timer Service"]
    end

    TC -->|"Create Quiz"| API --> QS --> QDB
    ST -->|"Take Quiz"| API --> SS --> SDB
    SS --> TM
    SS --> GS --> SDB
    GS --> LB --> CACHE

    style ST fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style TC fill:#50C878,stroke:#3A9A5C,color:#fff
    style API fill:#F5A623,stroke:#C4841C,color:#fff
    style QS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style SS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style GS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style LB fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style QDB fill:#E74C3C,stroke:#C0392B,color:#fff
    style SDB fill:#E74C3C,stroke:#C0392B,color:#fff
    style CACHE fill:#E67E22,stroke:#D35400,color:#fff
    style TM fill:#E67E22,stroke:#D35400,color:#fff
```

**Key Discussion Points:**
- Question bank design and randomization
- Timer management (server-side)
- Anti-cheating measures
- Auto-grading vs manual grading
- Real-time leaderboard updates

---

### 20. Design a User Authentication System

```mermaid
graph TB
    subgraph Client
        U["👤 User"]
        APP["Application"]
    end

    subgraph Auth Layer
        GW["API Gateway"]
        AS["Auth Service"]
    end

    subgraph Auth Methods
        EP["Email/Password"]
        OA["OAuth 2.0<br/>(Google, GitHub)"]
        MFA["MFA Service<br/>(TOTP/SMS)"]
    end

    subgraph Token Management
        JWT["JWT Issuer"]
        RT["Refresh Token Store"]
        BL["Token Blacklist"]
    end

    subgraph Data
        UDB[("User DB")]
        SDB[("Session Store")]
    end

    U --> APP --> GW --> AS
    AS --> EP
    AS --> OA
    AS --> MFA
    AS --> JWT
    JWT --> RT
    JWT --> BL
    AS --> UDB
    AS --> SDB

    style U fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style APP fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style GW fill:#F5A623,stroke:#C4841C,color:#fff
    style AS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style EP fill:#50C878,stroke:#3A9A5C,color:#fff
    style OA fill:#50C878,stroke:#3A9A5C,color:#fff
    style MFA fill:#50C878,stroke:#3A9A5C,color:#fff
    style JWT fill:#E67E22,stroke:#D35400,color:#fff
    style RT fill:#E67E22,stroke:#D35400,color:#fff
    style BL fill:#E67E22,stroke:#D35400,color:#fff
    style UDB fill:#E74C3C,stroke:#C0392B,color:#fff
    style SDB fill:#E74C3C,stroke:#C0392B,color:#fff
```

**Key Discussion Points:**
- Password hashing (bcrypt, Argon2)
- JWT vs session-based auth
- OAuth 2.0 / OpenID Connect flows
- MFA implementation (TOTP, WebAuthn)
- Rate limiting and brute-force protection

---

---

## Experienced Level System Design

> **Target Audience:** 3–15+ years of experience
> **Focus:** Distributed systems, fault tolerance, scalability, consistency models, data partitioning, advanced trade-offs

---

### 1. Design a URL Shortening Service like bit.ly

```mermaid
graph TB
    subgraph Global Users
        U1["👤 Region A Users"]
        U2["👤 Region B Users"]
    end

    subgraph Edge Layer
        CDN["Global CDN"]
        DNS["GeoDNS"]
    end

    subgraph API Layer
        LB1["Load Balancer R1"]
        LB2["Load Balancer R2"]
        API1["API Cluster R1"]
        API2["API Cluster R2"]
    end

    subgraph Core Services
        KG["Key Generation Service<br/>(Zookeeper Range)"]
        AS["Analytics Service"]
        RL["Rate Limiter"]
    end

    subgraph Data Layer
        DB1[("Primary DB<br/>(Sharded)")]
        DB2[("Read Replica")]
        CACHE["Distributed Cache<br/>(Redis Cluster)"]
        KAFKA["Kafka<br/>(Click Stream)"]
        DW[("Analytics DW")]
    end

    U1 --> DNS --> CDN
    U2 --> DNS
    CDN --> LB1 --> API1
    CDN --> LB2 --> API2
    API1 --> RL
    API1 --> KG
    API1 --> CACHE --> DB1
    API1 --> KAFKA --> DW
    DB1 --> DB2
    API2 --> CACHE
    API1 --> AS --> DW

    style U1 fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style U2 fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style CDN fill:#F5A623,stroke:#C4841C,color:#fff
    style DNS fill:#F5A623,stroke:#C4841C,color:#fff
    style LB1 fill:#F5A623,stroke:#C4841C,color:#fff
    style LB2 fill:#F5A623,stroke:#C4841C,color:#fff
    style API1 fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style API2 fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style KG fill:#50C878,stroke:#3A9A5C,color:#fff
    style AS fill:#50C878,stroke:#3A9A5C,color:#fff
    style RL fill:#50C878,stroke:#3A9A5C,color:#fff
    style DB1 fill:#E74C3C,stroke:#C0392B,color:#fff
    style DB2 fill:#E74C3C,stroke:#C0392B,color:#fff
    style CACHE fill:#E67E22,stroke:#D35400,color:#fff
    style KAFKA fill:#E67E22,stroke:#D35400,color:#fff
    style DW fill:#E74C3C,stroke:#C0392B,color:#fff
```

**Key Discussion Points:**
- Key generation: Zookeeper range allocation vs snowflake IDs
- Database sharding strategy (hash-based on short URL)
- 100:1 read-to-write ratio optimization
- Multi-region deployment with GeoDNS
- Click analytics pipeline (Kafka → data warehouse)
- Cache-aside pattern with Redis cluster
- URL expiration and cleanup jobs

---

### 2. Design a Distributed Key-Value Store like Redis

```mermaid
graph TB
    subgraph Client
        C["Client SDK"]
    end

    subgraph Routing
        PROXY["Smart Proxy"]
        HR["Consistent Hashing Ring"]
    end

    subgraph Cluster Nodes
        N1["Node 1<br/>(Primary)"]
        N2["Node 2<br/>(Primary)"]
        N3["Node 3<br/>(Primary)"]
        R1["Replica 1"]
        R2["Replica 2"]
        R3["Replica 3"]
    end

    subgraph Per-Node Architecture
        MEM["In-Memory Store<br/>(Hash Table)"]
        WAL["Write-Ahead Log"]
        SNAP["Snapshot (RDB)"]
        AOF["Append-Only File"]
    end

    subgraph Coordination
        GOS["Gossip Protocol"]
        FD["Failure Detector"]
        ELEC["Leader Election"]
    end

    C --> PROXY --> HR
    HR --> N1
    HR --> N2
    HR --> N3
    N1 --> R1
    N2 --> R2
    N3 --> R3
    N1 --> MEM
    MEM --> WAL
    WAL --> AOF
    WAL --> SNAP
    N1 <--> GOS
    N2 <--> GOS
    N3 <--> GOS
    GOS --> FD --> ELEC

    style C fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style PROXY fill:#F5A623,stroke:#C4841C,color:#fff
    style HR fill:#F5A623,stroke:#C4841C,color:#fff
    style N1 fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style N2 fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style N3 fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style R1 fill:#9B88FE,stroke:#7B68EE,color:#fff
    style R2 fill:#9B88FE,stroke:#7B68EE,color:#fff
    style R3 fill:#9B88FE,stroke:#7B68EE,color:#fff
    style MEM fill:#50C878,stroke:#3A9A5C,color:#fff
    style WAL fill:#50C878,stroke:#3A9A5C,color:#fff
    style SNAP fill:#E74C3C,stroke:#C0392B,color:#fff
    style AOF fill:#E74C3C,stroke:#C0392B,color:#fff
    style GOS fill:#E67E22,stroke:#D35400,color:#fff
    style FD fill:#E67E22,stroke:#D35400,color:#fff
    style ELEC fill:#E67E22,stroke:#D35400,color:#fff
```

**Key Discussion Points:**
- Consistent hashing with virtual nodes
- Replication strategies (leader-follower, quorum)
- Persistence: RDB snapshots vs AOF
- Memory management and eviction policies
- Gossip protocol for cluster membership
- Linearizability vs eventual consistency
- Data partitioning and resharding

---

### 3. Design a Scalable Social Network like Facebook

```mermaid
graph TB
    subgraph Users
        U["👤 2B+ Users"]
    end

    subgraph Edge
        CDN["Global CDN"]
        LB["L7 Load Balancer"]
    end

    subgraph API Layer
        GW["API Gateway"]
        GQL["GraphQL Federation"]
    end

    subgraph Core Services
        US["User Service"]
        FS["Feed Service"]
        GS["Graph Service<br/>(TAO)"]
        MS["Messenger"]
        NS["Notification"]
        SS["Search Service"]
        ADS["Ad Service"]
    end

    subgraph Feed Pipeline
        FW["Fan-out Service"]
        FR["Feed Ranker<br/>(ML)"]
        FC["Feed Cache"]
    end

    subgraph Data Tier
        MYSQL[("MySQL<br/>(Sharded)")]
        TAO["TAO Cache<br/>(Social Graph)"]
        MC["Memcached<br/>(Multi-Layer)"]
        HDFS["HDFS<br/>(Media)"]
        KAFKA["Kafka"]
        HIVE[("Hive DW")]
    end

    U --> CDN --> LB --> GW --> GQL
    GQL --> US
    GQL --> FS
    GQL --> GS
    GQL --> MS
    GQL --> SS
    GQL --> ADS
    FS --> FW --> FC
    FW --> FR
    US --> MYSQL
    GS --> TAO --> MYSQL
    FS --> MC
    FS --> KAFKA --> HIVE
    US --> HDFS

    style U fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style CDN fill:#F5A623,stroke:#C4841C,color:#fff
    style LB fill:#F5A623,stroke:#C4841C,color:#fff
    style GW fill:#F5A623,stroke:#C4841C,color:#fff
    style GQL fill:#F5A623,stroke:#C4841C,color:#fff
    style US fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style FS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style GS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style MS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style NS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style SS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style ADS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style FW fill:#50C878,stroke:#3A9A5C,color:#fff
    style FR fill:#50C878,stroke:#3A9A5C,color:#fff
    style FC fill:#50C878,stroke:#3A9A5C,color:#fff
    style MYSQL fill:#E74C3C,stroke:#C0392B,color:#fff
    style TAO fill:#E67E22,stroke:#D35400,color:#fff
    style MC fill:#E67E22,stroke:#D35400,color:#fff
    style HDFS fill:#E74C3C,stroke:#C0392B,color:#fff
    style KAFKA fill:#E67E22,stroke:#D35400,color:#fff
    style HIVE fill:#E74C3C,stroke:#C0392B,color:#fff
```

**Key Discussion Points:**
- Social graph storage (TAO — read-optimized graph cache)
- Fan-out on write (pre-compute feeds for active users) vs fan-out on read (celebrities)
- Multi-layer caching (L1 Memcached → L2 TAO → L3 MySQL)
- News feed ranking (ML-based relevance scoring)
- Sharding strategy for user data (user_id based)
- GraphQL federation for microservices
- Content moderation pipeline
- Privacy and access control at scale

---

### 4. Design a Scalable Recommendation System like Netflix

```mermaid
graph TB
    subgraph Users
        U["👤 200M+ Users"]
    end

    subgraph Online Serving
        API["API Gateway"]
        RS["Recommendation Server"]
        CACHE["Recommendation Cache"]
        AB["A/B Testing Framework"]
    end

    subgraph ML Pipeline
        FE["Feature Engineering"]
        CF["Collaborative Filtering<br/>(Matrix Factorization)"]
        DL["Deep Learning Models<br/>(Neural CF)"]
        CB["Content-Based Models"]
        EN["Ensemble / Re-Ranker"]
    end

    subgraph Offline Pipeline
        KAFKA["Kafka<br/>(User Events)"]
        SPARK["Spark<br/>(Batch Processing)"]
        FS["Feature Store"]
        MS["Model Store"]
    end

    subgraph Data
        VDB[("Video Catalog")]
        UDB[("User Profiles")]
        EDB[("Event History")]
    end

    U --> API --> RS
    RS --> CACHE
    RS --> AB
    RS --> EN
    EN --> CF
    EN --> DL
    EN --> CB
    CF --> FS
    DL --> MS
    U -->|"Watch/Rate/Browse"| KAFKA
    KAFKA --> SPARK
    SPARK --> FS
    SPARK --> MS
    FS --> UDB
    FS --> EDB
    CB --> VDB

    style U fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style API fill:#F5A623,stroke:#C4841C,color:#fff
    style RS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style CACHE fill:#E67E22,stroke:#D35400,color:#fff
    style AB fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style FE fill:#50C878,stroke:#3A9A5C,color:#fff
    style CF fill:#50C878,stroke:#3A9A5C,color:#fff
    style DL fill:#50C878,stroke:#3A9A5C,color:#fff
    style CB fill:#50C878,stroke:#3A9A5C,color:#fff
    style EN fill:#50C878,stroke:#3A9A5C,color:#fff
    style KAFKA fill:#E67E22,stroke:#D35400,color:#fff
    style SPARK fill:#E67E22,stroke:#D35400,color:#fff
    style FS fill:#E74C3C,stroke:#C0392B,color:#fff
    style MS fill:#E74C3C,stroke:#C0392B,color:#fff
    style VDB fill:#E74C3C,stroke:#C0392B,color:#fff
    style UDB fill:#E74C3C,stroke:#C0392B,color:#fff
    style EDB fill:#E74C3C,stroke:#C0392B,color:#fff
```

**Key Discussion Points:**
- Two-stage ranking: candidate generation → re-ranking
- Matrix factorization (ALS) and neural collaborative filtering
- Feature store for real-time and batch features
- A/B testing framework for model evaluation
- Cold start: onboarding quiz, popularity-based fallback
- Real-time personalization vs batch pre-computation
- Multi-objective optimization (engagement, diversity, freshness)

---

### 5. Design a Distributed File System like HDFS

```mermaid
graph TB
    subgraph Client
        C["HDFS Client"]
    end

    subgraph Master
        NN["NameNode<br/>(Metadata)"]
        SNN["Secondary NameNode<br/>(Checkpoint)"]
    end

    subgraph Data Nodes
        DN1["DataNode 1"]
        DN2["DataNode 2"]
        DN3["DataNode 3"]
        DN4["DataNode 4"]
    end

    subgraph Block Storage
        B1["Block A-1"]
        B2["Block A-2"]
        B3["Block A-3"]
    end

    subgraph Reliability
        REP["Replication Manager<br/>(Factor = 3)"]
        HB["Heartbeat Monitor"]
        RR["Rack-Aware Placement"]
    end

    C -->|"1. Get Block Locations"| NN
    NN -->|"2. Return DataNode List"| C
    C -->|"3. Read/Write Blocks"| DN1
    C -->|"3. Read/Write Blocks"| DN2
    NN --> SNN
    DN1 --> B1
    DN2 --> B2
    DN3 --> B3
    DN1 <-->|"Heartbeat"| HB
    DN2 <-->|"Heartbeat"| HB
    DN3 <-->|"Heartbeat"| HB
    DN4 <-->|"Heartbeat"| HB
    HB --> NN
    NN --> REP --> RR

    style C fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style NN fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style SNN fill:#9B88FE,stroke:#7B68EE,color:#fff
    style DN1 fill:#50C878,stroke:#3A9A5C,color:#fff
    style DN2 fill:#50C878,stroke:#3A9A5C,color:#fff
    style DN3 fill:#50C878,stroke:#3A9A5C,color:#fff
    style DN4 fill:#50C878,stroke:#3A9A5C,color:#fff
    style B1 fill:#E74C3C,stroke:#C0392B,color:#fff
    style B2 fill:#E74C3C,stroke:#C0392B,color:#fff
    style B3 fill:#E74C3C,stroke:#C0392B,color:#fff
    style REP fill:#E67E22,stroke:#D35400,color:#fff
    style HB fill:#E67E22,stroke:#D35400,color:#fff
    style RR fill:#F5A623,stroke:#C4841C,color:#fff
```

**Key Discussion Points:**
- NameNode as single point of failure → HA with Zookeeper
- Block size (128MB default), replication factor
- Rack-aware replica placement
- Write pipeline (chain replication)
- Edit log and checkpoint mechanism
- Data locality for MapReduce
- Handling NameNode memory limits (Federation)

---

### 6. Design a Real-Time Messaging System like WhatsApp

```mermaid
graph TB
    subgraph Users
        U1["👤 User A"]
        U2["👤 User B"]
    end

    subgraph Connection Layer
        GW1["WS Gateway 1"]
        GW2["WS Gateway 2"]
        CM["Connection Manager<br/>(User → Gateway Map)"]
    end

    subgraph Message Flow
        MR["Message Router"]
        MQ["Message Queue<br/>(per user)"]
        SEQ["Sequencer<br/>(Ordering)"]
    end

    subgraph Services
        GS["Group Service"]
        PS["Presence Service"]
        NS["Push Notification"]
        E2E["E2E Encryption<br/>(Signal Protocol)"]
    end

    subgraph Data
        CASS[("Cassandra<br/>(Messages)")]
        REDIS["Redis<br/>(Sessions)"]
        ZK["Zookeeper<br/>(Coordination)"]
    end

    U1 -->|"WebSocket"| GW1
    U2 -->|"WebSocket"| GW2
    GW1 --> MR
    MR --> CM
    MR --> MQ
    MQ --> SEQ --> CASS
    MR --> GW2
    GW1 --> E2E
    MR --> GS
    GW1 --> PS --> REDIS
    MR -->|"User Offline"| NS
    CM --> ZK

    style U1 fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style U2 fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style GW1 fill:#F5A623,stroke:#C4841C,color:#fff
    style GW2 fill:#F5A623,stroke:#C4841C,color:#fff
    style CM fill:#F5A623,stroke:#C4841C,color:#fff
    style MR fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style MQ fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style SEQ fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style GS fill:#50C878,stroke:#3A9A5C,color:#fff
    style PS fill:#50C878,stroke:#3A9A5C,color:#fff
    style NS fill:#50C878,stroke:#3A9A5C,color:#fff
    style E2E fill:#50C878,stroke:#3A9A5C,color:#fff
    style CASS fill:#E74C3C,stroke:#C0392B,color:#fff
    style REDIS fill:#E67E22,stroke:#D35400,color:#fff
    style ZK fill:#E67E22,stroke:#D35400,color:#fff
```

**Key Discussion Points:**
- End-to-end encryption (Signal Protocol / Double Ratchet)
- Connection management at scale (millions of concurrent WebSockets)
- Message delivery guarantees (at-least-once with dedup)
- Sent / Delivered / Read receipts
- Group messaging fan-out
- Offline message queue and push notifications
- Cassandra for message storage (partition by user_id + time)

---

### 7. Design a Web Crawler like Google

```mermaid
graph TB
    subgraph Seed
        SEED["Seed URLs"]
    end

    subgraph Frontier
        PQ["Priority Queue<br/>(URL Frontier)"]
        POL["Politeness Scheduler<br/>(per domain)"]
        DUP["URL Deduplicator<br/>(Bloom Filter)"]
    end

    subgraph Crawl Workers
        F1["Fetcher 1"]
        F2["Fetcher 2"]
        F3["Fetcher N"]
        DNS["DNS Resolver<br/>(Cache)"]
    end

    subgraph Processing
        HP["HTML Parser"]
        LE["Link Extractor"]
        CE["Content Extractor"]
        RD["Robot.txt Checker"]
    end

    subgraph Storage
        CS[("Content Store<br/>(Distributed)")]
        US[("URL DB")]
        IDX["Indexer Pipeline"]
    end

    SEED --> PQ
    PQ --> POL --> F1
    PQ --> POL --> F2
    PQ --> POL --> F3
    F1 --> DNS
    F1 --> HP
    HP --> RD
    HP --> LE --> DUP
    DUP -->|"New URLs"| PQ
    HP --> CE --> CS
    CE --> IDX
    DUP --> US

    style SEED fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style PQ fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style POL fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style DUP fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style F1 fill:#F5A623,stroke:#C4841C,color:#fff
    style F2 fill:#F5A623,stroke:#C4841C,color:#fff
    style F3 fill:#F5A623,stroke:#C4841C,color:#fff
    style DNS fill:#F5A623,stroke:#C4841C,color:#fff
    style HP fill:#50C878,stroke:#3A9A5C,color:#fff
    style LE fill:#50C878,stroke:#3A9A5C,color:#fff
    style CE fill:#50C878,stroke:#3A9A5C,color:#fff
    style RD fill:#50C878,stroke:#3A9A5C,color:#fff
    style CS fill:#E74C3C,stroke:#C0392B,color:#fff
    style US fill:#E74C3C,stroke:#C0392B,color:#fff
    style IDX fill:#E67E22,stroke:#D35400,color:#fff
```

**Key Discussion Points:**
- URL frontier with priority (PageRank, freshness)
- Politeness: per-domain rate limiting, robots.txt
- URL deduplication (Bloom filter, content fingerprinting)
- Distributed crawling across thousands of machines
- Handling traps (infinite URLs, spider traps)
- Incremental crawl vs full re-crawl
- Content change detection and recrawl scheduling

---

### 8. Design a Distributed Cache like Memcached

```mermaid
graph TB
    subgraph Clients
        C1["App Server 1"]
        C2["App Server 2"]
        C3["App Server N"]
    end

    subgraph Client Library
        CH["Consistent Hashing"]
        CP["Connection Pool"]
    end

    subgraph Cache Cluster
        M1["Memcached Node 1"]
        M2["Memcached Node 2"]
        M3["Memcached Node 3"]
        M4["Memcached Node 4"]
    end

    subgraph Per-Node
        SLAB["Slab Allocator"]
        HT["Hash Table"]
        LRU["LRU Eviction"]
    end

    subgraph Backend
        DB[("Database<br/>(Source of Truth)")]
    end

    C1 --> CH
    C2 --> CH
    C3 --> CH
    CH --> CP
    CP --> M1
    CP --> M2
    CP --> M3
    CP --> M4
    M1 --> SLAB --> HT --> LRU
    C1 -->|"Cache Miss"| DB

    style C1 fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style C2 fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style C3 fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style CH fill:#F5A623,stroke:#C4841C,color:#fff
    style CP fill:#F5A623,stroke:#C4841C,color:#fff
    style M1 fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style M2 fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style M3 fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style M4 fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style SLAB fill:#50C878,stroke:#3A9A5C,color:#fff
    style HT fill:#50C878,stroke:#3A9A5C,color:#fff
    style LRU fill:#50C878,stroke:#3A9A5C,color:#fff
    style DB fill:#E74C3C,stroke:#C0392B,color:#fff
```

**Key Discussion Points:**
- Consistent hashing for key distribution
- Slab allocator for memory management
- Cache invalidation strategies (TTL, write-through, write-behind)
- Thundering herd / cache stampede prevention
- Hot key problem and mitigation
- No inter-node communication (shared-nothing)
- Failure handling: rehashing without cascading misses

---

### 9. Design a CDN like Cloudflare

```mermaid
graph TB
    subgraph Users
        U1["👤 US User"]
        U2["👤 EU User"]
        U3["👤 Asia User"]
    end

    subgraph DNS
        GDNS["Anycast DNS"]
    end

    subgraph Edge PoPs
        E1["PoP US-East"]
        E2["PoP EU-West"]
        E3["PoP Asia-South"]
    end

    subgraph Edge Services
        EC["Edge Cache"]
        WAF["WAF / DDoS Shield"]
        SSL["TLS Termination"]
        COMP["Compression<br/>(Brotli/gzip)"]
    end

    subgraph Mid-Tier
        MT["Mid-Tier Cache<br/>(Shield)"]
    end

    subgraph Origin
        OR["Origin Server"]
        OS["Origin Storage"]
    end

    U1 --> GDNS --> E1
    U2 --> GDNS --> E2
    U3 --> GDNS --> E3
    E1 --> SSL --> WAF --> EC
    EC -->|"Cache Miss"| MT
    MT -->|"Miss"| OR --> OS
    E2 --> EC
    E3 --> EC
    EC --> COMP

    style U1 fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style U2 fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style U3 fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style GDNS fill:#F5A623,stroke:#C4841C,color:#fff
    style E1 fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style E2 fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style E3 fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style EC fill:#50C878,stroke:#3A9A5C,color:#fff
    style WAF fill:#50C878,stroke:#3A9A5C,color:#fff
    style SSL fill:#50C878,stroke:#3A9A5C,color:#fff
    style COMP fill:#50C878,stroke:#3A9A5C,color:#fff
    style MT fill:#E67E22,stroke:#D35400,color:#fff
    style OR fill:#E74C3C,stroke:#C0392B,color:#fff
    style OS fill:#E74C3C,stroke:#C0392B,color:#fff
```

**Key Discussion Points:**
- Anycast routing for nearest PoP selection
- Cache hierarchy: Edge → Shield (mid-tier) → Origin
- Cache invalidation and purge APIs
- TLS termination at edge
- DDoS mitigation and WAF rules
- Content compression (Brotli, gzip)
- Edge computing (Workers/Functions at edge)
- Cache key design and vary headers

---

### 10. Design a Scalable Search Engine like Google

```mermaid
graph TB
    subgraph User
        U["👤 User"]
    end

    subgraph Query Path
        QP["Query Processor"]
        QR["Query Rewriter<br/>(Synonyms, Spell)"]
        SC["Shard Coordinator"]
    end

    subgraph Index Cluster
        IS1["Index Shard 1"]
        IS2["Index Shard 2"]
        IS3["Index Shard N"]
    end

    subgraph Ranking
        PR["PageRank"]
        ML["ML Ranker<br/>(LambdaMART)"]
        PS["Personalization"]
        SN["Snippet Generator"]
    end

    subgraph Indexing Pipeline
        CR["Distributed Crawler"]
        PP["Document Processor"]
        IB["Index Builder<br/>(MapReduce)"]
    end

    subgraph Storage
        BT["Bigtable<br/>(Web Index)"]
        GFS["GFS<br/>(Raw Pages)"]
        KG["Knowledge Graph"]
    end

    U --> QP --> QR --> SC
    SC --> IS1
    SC --> IS2
    SC --> IS3
    IS1 --> ML
    IS2 --> ML
    ML --> PR
    ML --> PS
    ML --> SN --> U
    CR --> PP --> IB
    IB --> BT
    IB --> IS1
    PP --> GFS
    ML --> KG

    style U fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style QP fill:#F5A623,stroke:#C4841C,color:#fff
    style QR fill:#F5A623,stroke:#C4841C,color:#fff
    style SC fill:#F5A623,stroke:#C4841C,color:#fff
    style IS1 fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style IS2 fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style IS3 fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style PR fill:#50C878,stroke:#3A9A5C,color:#fff
    style ML fill:#50C878,stroke:#3A9A5C,color:#fff
    style PS fill:#50C878,stroke:#3A9A5C,color:#fff
    style SN fill:#50C878,stroke:#3A9A5C,color:#fff
    style CR fill:#E67E22,stroke:#D35400,color:#fff
    style PP fill:#E67E22,stroke:#D35400,color:#fff
    style IB fill:#E67E22,stroke:#D35400,color:#fff
    style BT fill:#E74C3C,stroke:#C0392B,color:#fff
    style GFS fill:#E74C3C,stroke:#C0392B,color:#fff
    style KG fill:#E74C3C,stroke:#C0392B,color:#fff
```

**Key Discussion Points:**
- Inverted index partitioning (document vs term partitioned)
- PageRank computation (iterative MapReduce)
- Multi-signal ranking (freshness, authority, relevance, personalization)
- Query auto-complete with trie + top-k
- Knowledge graph integration
- Serving latency budget (scatter-gather across shards)
- Index serving: real-time vs batch indexing
- Spell correction and query understanding

---

### 11. Design a Ride-Sharing System like Uber

```mermaid
graph TB
    subgraph Users
        R["👤 Riders"]
        D["🚗 Drivers (Millions)"]
    end

    subgraph Gateway
        LB["L4/L7 Load Balancer"]
        API["API Gateway"]
    end

    subgraph Core Services
        DM["Dispatch / Matching<br/>(DISCO)"]
        LS["Location Service<br/>(Ringpop)"]
        TS["Trip Service"]
        PS["Pricing Service<br/>(Surge)"]
        ETA["ETA Service"]
        PAY["Payment Service"]
    end

    subgraph Location Platform
        GEO["Geospatial Index<br/>(H3 / S2 Cells)"]
        CELL["Cell-Based Partitioning"]
    end

    subgraph Data
        CASS[("Cassandra")]
        MYSQL[("MySQL<br/>(Schemaless)")]
        REDIS["Redis<br/>(Driver Locations)"]
        KAFKA["Kafka"]
        HIVE[("Hive / Presto)"]
    end

    R --> LB --> API
    D --> LB
    API --> DM
    API --> LS --> GEO --> CELL
    API --> TS --> CASS
    API --> PS
    API --> ETA
    DM --> GEO
    DM --> REDIS
    TS --> PAY
    TS --> MYSQL
    LS --> KAFKA --> HIVE

    style R fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style D fill:#50C878,stroke:#3A9A5C,color:#fff
    style LB fill:#F5A623,stroke:#C4841C,color:#fff
    style API fill:#F5A623,stroke:#C4841C,color:#fff
    style DM fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style LS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style TS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style PS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style ETA fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style PAY fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style GEO fill:#50C878,stroke:#3A9A5C,color:#fff
    style CELL fill:#50C878,stroke:#3A9A5C,color:#fff
    style CASS fill:#E74C3C,stroke:#C0392B,color:#fff
    style MYSQL fill:#E74C3C,stroke:#C0392B,color:#fff
    style REDIS fill:#E67E22,stroke:#D35400,color:#fff
    style KAFKA fill:#E67E22,stroke:#D35400,color:#fff
    style HIVE fill:#E74C3C,stroke:#C0392B,color:#fff
```

**Key Discussion Points:**
- Geospatial indexing: H3/S2 cells for efficient proximity queries
- DISCO matching engine (supply-demand optimization)
- Real-time location ingestion at scale (millions of updates/sec)
- Surge pricing algorithm (dynamic demand-supply)
- ETA prediction (ML on road graphs)
- Trip state machine and idempotency
- Ringpop for consistent hashing of location service
- Schemaless (Uber's MySQL wrapper for flexible schema)

---

### 12. Design a Video Streaming Service like YouTube

```mermaid
graph TB
    subgraph Users
        V["👤 Viewers<br/>(2B+ Monthly)"]
        C["🎬 Creators"]
    end

    subgraph Edge
        CDN["Global CDN<br/>(Google Edge)"]
    end

    subgraph Upload Pipeline
        UP["Upload Service"]
        TC["Transcoding Farm<br/>(FFmpeg Workers)"]
        TH["Thumbnail Generator"]
        AI["Content Moderation<br/>(ML Pipeline)"]
    end

    subgraph Serving
        VS["Video Serving"]
        SS["Search / Discovery"]
        RS["Recommendation<br/>(Deep Neural Net)"]
        CS["Comment Service"]
        LS["Like / Subscribe"]
        ADS["Ad Insertion<br/>(VAST/VPAID)"]
    end

    subgraph Data
        BT[("Bigtable<br/>(Metadata)")]
        BLOB["Colossus/GFS<br/>(Video Blobs)"]
        VQ["Video Queue<br/>(Pub/Sub)"]
        MYSQL[("Vitess<br/>(MySQL Sharded)")]
        ML["ML Feature Store"]
    end

    C -->|"Upload"| UP --> VQ
    VQ --> TC --> BLOB
    VQ --> TH
    VQ --> AI
    UP --> BT
    V --> CDN --> BLOB
    V --> SS --> BT
    V --> RS --> ML
    V --> CS --> MYSQL
    V --> LS --> MYSQL
    V --> ADS

    style V fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style C fill:#50C878,stroke:#3A9A5C,color:#fff
    style CDN fill:#F5A623,stroke:#C4841C,color:#fff
    style UP fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style TC fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style TH fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style AI fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style VS fill:#50C878,stroke:#3A9A5C,color:#fff
    style SS fill:#50C878,stroke:#3A9A5C,color:#fff
    style RS fill:#50C878,stroke:#3A9A5C,color:#fff
    style CS fill:#50C878,stroke:#3A9A5C,color:#fff
    style LS fill:#50C878,stroke:#3A9A5C,color:#fff
    style ADS fill:#50C878,stroke:#3A9A5C,color:#fff
    style BT fill:#E74C3C,stroke:#C0392B,color:#fff
    style BLOB fill:#E74C3C,stroke:#C0392B,color:#fff
    style VQ fill:#E67E22,stroke:#D35400,color:#fff
    style MYSQL fill:#E74C3C,stroke:#C0392B,color:#fff
    style ML fill:#E67E22,stroke:#D35400,color:#fff
```

**Key Discussion Points:**
- Video transcoding pipeline (resolution, codec, bitrate)
- Adaptive bitrate streaming (DASH/HLS)
- Vitess for horizontally sharded MySQL
- Recommendation: Two-tower model, candidate generation → ranking
- Content moderation (CSAM detection, copyright — Content ID)
- Ad insertion and monetization (pre-roll, mid-roll)
- Live streaming architecture
- View count accuracy at scale (approximate counting)

---

### 13. Design an Online Food Delivery System like Zomato

```mermaid
graph TB
    subgraph Users
        CU["👤 Customers"]
        RE["🍕 Restaurants"]
        DA["🛵 Delivery Partners"]
    end

    subgraph Gateway
        LB["Load Balancer"]
        API["API Gateway"]
    end

    subgraph Core Services
        RS["Restaurant Service"]
        MS["Menu Service"]
        OS["Order Orchestrator"]
        DS["Delivery Service"]
        LS["Location Service"]
        PS["Pricing / Promo"]
        PAY["Payment Service"]
        RT["Rating / Review"]
        NS["Notification Hub"]
    end

    subgraph Real-Time
        WS["WebSocket Gateway"]
        KAFKA["Kafka<br/>(Order Events)"]
        GEO["Geospatial Index<br/>(Geohash)"]
    end

    subgraph Data
        PG[("PostgreSQL<br/>(Orders)")]
        ES["Elasticsearch<br/>(Restaurant Search)"]
        REDIS["Redis<br/>(Sessions, Cart)"]
        MONGO[("MongoDB<br/>(Menu Catalog)")]
        S3["Object Store<br/>(Images)"]
    end

    CU --> LB --> API
    RE --> API
    DA --> WS
    API --> RS --> ES
    API --> MS --> MONGO
    API --> OS --> PG
    OS --> DS --> GEO
    OS --> PAY
    OS --> KAFKA --> NS
    DS --> LS --> GEO
    API --> PS
    API --> RT --> PG
    WS --> REDIS
    MS --> S3

    style CU fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style RE fill:#50C878,stroke:#3A9A5C,color:#fff
    style DA fill:#E67E22,stroke:#D35400,color:#fff
    style LB fill:#F5A623,stroke:#C4841C,color:#fff
    style API fill:#F5A623,stroke:#C4841C,color:#fff
    style RS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style MS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style OS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style DS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style LS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style PS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style PAY fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style RT fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style NS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style WS fill:#F5A623,stroke:#C4841C,color:#fff
    style KAFKA fill:#E67E22,stroke:#D35400,color:#fff
    style GEO fill:#50C878,stroke:#3A9A5C,color:#fff
    style PG fill:#E74C3C,stroke:#C0392B,color:#fff
    style ES fill:#E74C3C,stroke:#C0392B,color:#fff
    style REDIS fill:#E67E22,stroke:#D35400,color:#fff
    style MONGO fill:#E74C3C,stroke:#C0392B,color:#fff
    style S3 fill:#E74C3C,stroke:#C0392B,color:#fff
```

**Key Discussion Points:**
- Three-sided marketplace orchestration
- Order state machine (placed → accepted → preparing → picked → delivered)
- Delivery partner allocation (geohash-based proximity + availability)
- Restaurant search with Elasticsearch (location, cuisine, rating)
- Real-time order tracking (WebSocket + location streaming)
- Dynamic delivery fee calculation
- Peak load handling (lunch/dinner surges)
- Loyalty programs and promo engine

---

### 14. Design a Collaborative Document Editing System like Google Docs

```mermaid
graph TB
    subgraph Users
        U1["👤 User A"]
        U2["👤 User B"]
        U3["👤 User C"]
    end

    subgraph Connection
        WS["WebSocket Server"]
        SR["Session Router"]
    end

    subgraph Collaboration Engine
        OT["OT Engine<br/>(Operational Transform)"]
        CS["Cursor Sync"]
        VS["Version Service"]
    end

    subgraph Services
        DS["Document Service"]
        PS["Permission Service"]
        CM["Comment Service"]
        HS["History / Revision"]
    end

    subgraph Data
        DOC[("Document Store<br/>(Structured)")]
        OPS[("Operation Log")]
        BLOB["Asset Storage"]
        CACHE["Active Doc Cache"]
    end

    U1 -->|"WebSocket"| WS
    U2 -->|"WebSocket"| WS
    U3 -->|"WebSocket"| WS
    WS --> SR
    SR --> OT
    OT --> CS
    OT --> VS
    OT --> OPS
    SR --> DS --> DOC
    DS --> PS
    DS --> CM
    DS --> HS --> OPS
    OT --> CACHE
    DS --> BLOB

    style U1 fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style U2 fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style U3 fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style WS fill:#F5A623,stroke:#C4841C,color:#fff
    style SR fill:#F5A623,stroke:#C4841C,color:#fff
    style OT fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style CS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style VS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style DS fill:#50C878,stroke:#3A9A5C,color:#fff
    style PS fill:#50C878,stroke:#3A9A5C,color:#fff
    style CM fill:#50C878,stroke:#3A9A5C,color:#fff
    style HS fill:#50C878,stroke:#3A9A5C,color:#fff
    style DOC fill:#E74C3C,stroke:#C0392B,color:#fff
    style OPS fill:#E74C3C,stroke:#C0392B,color:#fff
    style BLOB fill:#E74C3C,stroke:#C0392B,color:#fff
    style CACHE fill:#E67E22,stroke:#D35400,color:#fff
```

**Key Discussion Points:**
- Operational Transformation (OT) vs CRDT for real-time collaboration
- Operation serialization and transformation functions
- Cursor position tracking and broadcasting
- Version history and revision diffing
- Conflict-free merging of concurrent edits
- Document permission model (viewer, commenter, editor)
- Session affinity (sticky sessions for active documents)
- Offline editing and reconciliation

---

### 15. Design an E-Commerce Platform like Amazon

```mermaid
graph TB
    subgraph Users
        B["👤 Buyers"]
        S["🏪 Sellers"]
    end

    subgraph Edge
        CDN["CDN"]
        LB["Load Balancer"]
    end

    subgraph API
        GW["API Gateway"]
        BFF["BFF Layer"]
    end

    subgraph Product Domain
        PC["Product Catalog"]
        SR["Search<br/>(Elasticsearch)"]
        RV["Reviews"]
    end

    subgraph Order Domain
        CT["Cart Service"]
        OD["Order Service"]
        INV["Inventory<br/>(Distributed Lock)"]
        PAY["Payment<br/>(Saga Pattern)"]
        SH["Shipping"]
    end

    subgraph Platform
        REC["Recommendation"]
        PR["Pricing Engine"]
        NS["Notification"]
        FP["Fraud Detection"]
    end

    subgraph Data
        DDB[("DynamoDB<br/>(Cart, Session)")]
        RDS[("RDS<br/>(Orders)")]
        ES["Elasticsearch"]
        S3["S3<br/>(Product Images)"]
        SQS["SQS / SNS"]
        KAFKA["Kafka"]
    end

    B --> CDN --> LB --> GW --> BFF
    S --> GW
    BFF --> PC --> ES
    BFF --> SR --> ES
    BFF --> CT --> DDB
    BFF --> OD --> RDS
    OD --> INV
    OD --> PAY
    OD --> SH
    OD --> SQS --> NS
    BFF --> REC
    BFF --> RV --> RDS
    PC --> S3
    OD --> KAFKA --> FP

    style B fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style S fill:#50C878,stroke:#3A9A5C,color:#fff
    style CDN fill:#F5A623,stroke:#C4841C,color:#fff
    style LB fill:#F5A623,stroke:#C4841C,color:#fff
    style GW fill:#F5A623,stroke:#C4841C,color:#fff
    style BFF fill:#F5A623,stroke:#C4841C,color:#fff
    style PC fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style SR fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style RV fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style CT fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style OD fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style INV fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style PAY fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style SH fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style REC fill:#50C878,stroke:#3A9A5C,color:#fff
    style PR fill:#50C878,stroke:#3A9A5C,color:#fff
    style NS fill:#50C878,stroke:#3A9A5C,color:#fff
    style FP fill:#50C878,stroke:#3A9A5C,color:#fff
    style DDB fill:#E74C3C,stroke:#C0392B,color:#fff
    style RDS fill:#E74C3C,stroke:#C0392B,color:#fff
    style ES fill:#E74C3C,stroke:#C0392B,color:#fff
    style S3 fill:#E74C3C,stroke:#C0392B,color:#fff
    style SQS fill:#E67E22,stroke:#D35400,color:#fff
    style KAFKA fill:#E67E22,stroke:#D35400,color:#fff
```

**Key Discussion Points:**
- Saga pattern for distributed order transactions
- Inventory management (pessimistic lock, reservation TTL)
- Search relevance (Elasticsearch with boosting)
- BFF (Backend for Frontend) pattern
- Event-driven architecture (order events → shipping, notification, analytics)
- Fraud detection pipeline
- Flash sale / high-traffic handling
- Multi-seller marketplace catalog

---

### 16. Design a Recommendation System for an Online Marketplace

```mermaid
graph TB
    subgraph Users
        U["👤 Users"]
    end

    subgraph Real-Time Layer
        API["API Server"]
        RTS["Real-Time Scoring"]
        FC["Feature Cache"]
    end

    subgraph Candidate Generation
        CG1["Similar Items<br/>(Item-Item CF)"]
        CG2["User History<br/>(User-Item CF)"]
        CG3["Trending / Popular"]
        CG4["Search-Based<br/>(Query Expansion)"]
    end

    subgraph Ranking
        L2R["Learning to Rank<br/>(GBDT/DNN)"]
        DIV["Diversity Filter"]
        BUS["Business Rules<br/>(Boost/Suppress)"]
    end

    subgraph Offline
        SPARK["Spark<br/>(Feature Engineering)"]
        TRAIN["Model Training<br/>(GPU Cluster)"]
        EMB["Embedding Generation<br/>(ANN Index)"]
    end

    subgraph Data
        EVENT[("Event Store<br/>(Clicks, Purchases)")]
        ITEM[("Item Catalog")]
        FSTORE["Feature Store"]
        ANN["ANN Index<br/>(FAISS/ScaNN)"]
    end

    U --> API --> RTS
    RTS --> FC --> FSTORE
    RTS --> CG1
    RTS --> CG2
    RTS --> CG3
    RTS --> CG4
    CG1 --> ANN
    CG2 --> ANN
    CG1 --> L2R --> DIV --> BUS --> U
    U -->|"Events"| EVENT
    EVENT --> SPARK --> FSTORE
    SPARK --> TRAIN
    TRAIN --> EMB --> ANN
    CG3 --> ITEM

    style U fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style API fill:#F5A623,stroke:#C4841C,color:#fff
    style RTS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style FC fill:#E67E22,stroke:#D35400,color:#fff
    style CG1 fill:#50C878,stroke:#3A9A5C,color:#fff
    style CG2 fill:#50C878,stroke:#3A9A5C,color:#fff
    style CG3 fill:#50C878,stroke:#3A9A5C,color:#fff
    style CG4 fill:#50C878,stroke:#3A9A5C,color:#fff
    style L2R fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style DIV fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style BUS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style SPARK fill:#E67E22,stroke:#D35400,color:#fff
    style TRAIN fill:#E67E22,stroke:#D35400,color:#fff
    style EMB fill:#E67E22,stroke:#D35400,color:#fff
    style EVENT fill:#E74C3C,stroke:#C0392B,color:#fff
    style ITEM fill:#E74C3C,stroke:#C0392B,color:#fff
    style FSTORE fill:#E74C3C,stroke:#C0392B,color:#fff
    style ANN fill:#E74C3C,stroke:#C0392B,color:#fff
```

**Key Discussion Points:**
- Multi-stage ranking pipeline (retrieval → scoring → re-ranking)
- Embedding-based retrieval with ANN (FAISS, ScaNN)
- Feature store for real-time and batch features
- Diversity and exploration-exploitation trade-off
- Business rules layer (promoted items, suppressed items)
- A/B testing and interleaving experiments
- Cold start for new items and new users
- Real-time feedback loop

---

### 17. Design a Fault-Tolerant Distributed Database System

```mermaid
graph TB
    subgraph Client
        C["Client Application"]
    end

    subgraph Query Layer
        QC["Query Coordinator"]
        QO["Query Optimizer"]
        QE["Query Executor"]
    end

    subgraph Consensus Layer
        L["Leader"]
        F1["Follower 1"]
        F2["Follower 2"]
        RAFT["Raft / Paxos<br/>Consensus"]
    end

    subgraph Storage Engine
        LSM["LSM Tree<br/>(Write Path)"]
        BT["B+ Tree<br/>(Read Path)"]
        WAL["Write-Ahead Log"]
        SS["SSTables"]
    end

    subgraph Distributed Features
        SH["Sharding<br/>(Range / Hash)"]
        REP["Replication<br/>(Quorum)"]
        TX["Distributed Txn<br/>(2PC / TrueTime)"]
        FD["Failure Detector<br/>(Phi Accrual)"]
    end

    C --> QC --> QO --> QE
    QE --> L
    L --> RAFT
    RAFT --> F1
    RAFT --> F2
    L --> LSM --> WAL
    LSM --> SS
    QE --> BT --> SS
    L --> SH
    L --> REP
    QE --> TX
    RAFT --> FD

    style C fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style QC fill:#F5A623,stroke:#C4841C,color:#fff
    style QO fill:#F5A623,stroke:#C4841C,color:#fff
    style QE fill:#F5A623,stroke:#C4841C,color:#fff
    style L fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style F1 fill:#9B88FE,stroke:#7B68EE,color:#fff
    style F2 fill:#9B88FE,stroke:#7B68EE,color:#fff
    style RAFT fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style LSM fill:#50C878,stroke:#3A9A5C,color:#fff
    style BT fill:#50C878,stroke:#3A9A5C,color:#fff
    style WAL fill:#50C878,stroke:#3A9A5C,color:#fff
    style SS fill:#E74C3C,stroke:#C0392B,color:#fff
    style SH fill:#E67E22,stroke:#D35400,color:#fff
    style REP fill:#E67E22,stroke:#D35400,color:#fff
    style TX fill:#E67E22,stroke:#D35400,color:#fff
    style FD fill:#E67E22,stroke:#D35400,color:#fff
```

**Key Discussion Points:**
- Raft vs Paxos consensus protocol
- LSM Tree vs B+ Tree storage engine trade-offs
- Write-ahead logging for durability
- Quorum reads/writes (R + W > N)
- Distributed transactions (2PC, Spanner's TrueTime)
- Sharding strategies (range vs hash partitioning)
- Failure detection (Phi Accrual failure detector)
- Linearizability, serializability, and snapshot isolation
- Compaction strategies for LSM trees

---

### 18. Design a Scalable Event-Driven System like Twitter

```mermaid
graph TB
    subgraph Users
        U["👤 330M+ Users"]
    end

    subgraph Edge
        CDN["CDN"]
        LB["Load Balancer"]
    end

    subgraph Write Path
        TS["Tweet Service"]
        FO["Fan-Out Service"]
        TL["Timeline Service"]
    end

    subgraph Read Path
        TLC["Timeline Cache<br/>(Redis)"]
        MR["Tweet Merger"]
        RK["Ranker"]
    end

    subgraph Services
        SS["Search Service<br/>(Earlybird)"]
        TR["Trends Service"]
        NS["Notification"]
        MM["Media Service"]
    end

    subgraph Data
        MYSQL[("MySQL<br/>(Tweets, Users)")]
        REDIS["Redis Cluster<br/>(Timelines)"]
        MH["Manhattan<br/>(KV Store)"]
        KAFKA["Kafka<br/>(Event Bus)"]
        ES["Earlybird<br/>(Real-Time Index)"]
        BLOB["Blob Store"]
    end

    U --> CDN --> LB
    LB --> TS --> MYSQL
    TS --> FO --> REDIS
    TS --> KAFKA
    KAFKA --> SS --> ES
    KAFKA --> TR
    KAFKA --> NS
    U -->|"Read Timeline"| TLC
    TLC --> MR --> RK --> U
    MR --> MYSQL
    TS --> MM --> BLOB
    FO --> MH

    style U fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style CDN fill:#F5A623,stroke:#C4841C,color:#fff
    style LB fill:#F5A623,stroke:#C4841C,color:#fff
    style TS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style FO fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style TL fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style TLC fill:#50C878,stroke:#3A9A5C,color:#fff
    style MR fill:#50C878,stroke:#3A9A5C,color:#fff
    style RK fill:#50C878,stroke:#3A9A5C,color:#fff
    style SS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style TR fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style NS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style MM fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style MYSQL fill:#E74C3C,stroke:#C0392B,color:#fff
    style REDIS fill:#E67E22,stroke:#D35400,color:#fff
    style MH fill:#E74C3C,stroke:#C0392B,color:#fff
    style KAFKA fill:#E67E22,stroke:#D35400,color:#fff
    style ES fill:#E74C3C,stroke:#C0392B,color:#fff
    style BLOB fill:#E74C3C,stroke:#C0392B,color:#fff
```

**Key Discussion Points:**
- Hybrid fan-out: pre-compute for regular users, merge on read for celebrities
- Twitter's Earlybird: real-time inverted index for tweet search
- Manhattan KV store for timeline storage
- Trend detection (sliding window + count-min sketch)
- Event sourcing with Kafka
- Tweet ranking algorithm (recency, engagement, relevance)
- Handling celebrity tweets (millions of followers)
- Rate limiting and abuse prevention

---

### 19. Design a Scalable Photo-Sharing Platform like Instagram

```mermaid
graph TB
    subgraph Users
        U["👤 2B+ Users"]
    end

    subgraph Edge
        CDN["CDN<br/>(Photo Delivery)"]
        LB["Load Balancer"]
    end

    subgraph Services
        US["Upload Service"]
        FS["Feed Service"]
        SS["Stories Service"]
        EX["Explore / Discover"]
        LS["Like / Comment"]
        DM["Direct Messages"]
        NS["Notification"]
    end

    subgraph Media Pipeline
        IP["Image Processor<br/>(Resize, Filter)"]
        ML["ML Pipeline<br/>(Tags, Moderation)"]
    end

    subgraph Feed Generation
        FO["Fan-Out Service"]
        FC["Feed Cache"]
        RK["ML Ranker"]
    end

    subgraph Data
        CASS[("Cassandra<br/>(Feed, Activity)")]
        PG[("PostgreSQL<br/>(Users, Relations)")]
        REDIS["Redis<br/>(Feed Cache)"]
        S3["S3<br/>(Photos/Videos)"]
        KAFKA["Kafka"]
    end

    U --> CDN --> S3
    U --> LB
    LB --> US --> IP --> S3
    IP --> ML
    US --> KAFKA
    LB --> FS --> FC --> REDIS
    KAFKA --> FO --> CASS
    FO --> FC
    LB --> SS --> CASS
    LB --> EX --> RK
    LB --> LS --> CASS
    LB --> DM
    KAFKA --> NS
    LS --> PG

    style U fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style CDN fill:#F5A623,stroke:#C4841C,color:#fff
    style LB fill:#F5A623,stroke:#C4841C,color:#fff
    style US fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style FS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style SS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style EX fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style LS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style DM fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style NS fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style IP fill:#50C878,stroke:#3A9A5C,color:#fff
    style ML fill:#50C878,stroke:#3A9A5C,color:#fff
    style FO fill:#50C878,stroke:#3A9A5C,color:#fff
    style FC fill:#50C878,stroke:#3A9A5C,color:#fff
    style RK fill:#50C878,stroke:#3A9A5C,color:#fff
    style CASS fill:#E74C3C,stroke:#C0392B,color:#fff
    style PG fill:#E74C3C,stroke:#C0392B,color:#fff
    style REDIS fill:#E67E22,stroke:#D35400,color:#fff
    style S3 fill:#E74C3C,stroke:#C0392B,color:#fff
    style KAFKA fill:#E67E22,stroke:#D35400,color:#fff
```

**Key Discussion Points:**
- Photo storage and CDN delivery (multiple resolutions)
- Cassandra for feed and activity streams
- PostgreSQL for relational data (users, follow graph)
- Feed ranking (ML-based interest prediction)
- Stories: ephemeral content with 24h TTL
- Explore page: interest-based discovery (embedding similarity)
- Image processing pipeline (filters, resizing, EXIF stripping)
- Content moderation (nudity detection, spam, hate speech)

---

### 20. Design a Distributed Task Scheduling System

```mermaid
graph TB
    subgraph Clients
        C1["Service A"]
        C2["Service B"]
        C3["Cron Jobs"]
    end

    subgraph Scheduler
        API["Scheduler API"]
        SC["Schedule Manager"]
        PQ["Priority Queue<br/>(Delay Queue)"]
    end

    subgraph Coordination
        ZK["Zookeeper<br/>(Leader Election)"]
        LDR["Leader Scheduler"]
        FD["Failure Detector"]
    end

    subgraph Workers
        W1["Worker Pool 1"]
        W2["Worker Pool 2"]
        W3["Worker Pool N"]
        HB["Heartbeat Manager"]
    end

    subgraph Data
        TDB[("Task DB<br/>(State Machine)")]
        DLQ["Dead Letter Queue"]
        LOG[("Execution Log")]
        MQ["Task Queue<br/>(Partitioned)"]
    end

    C1 --> API
    C2 --> API
    C3 --> API
    API --> SC --> PQ
    SC --> ZK --> LDR
    LDR --> MQ
    MQ --> W1
    MQ --> W2
    MQ --> W3
    W1 --> HB --> FD
    W1 -->|"Complete/Fail"| TDB
    FD -->|"Worker Dead"| MQ
    TDB -->|"Failed Tasks"| DLQ
    W1 --> LOG

    style C1 fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style C2 fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style C3 fill:#4A90D9,stroke:#2C5F8A,color:#fff
    style API fill:#F5A623,stroke:#C4841C,color:#fff
    style SC fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style PQ fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style ZK fill:#E67E22,stroke:#D35400,color:#fff
    style LDR fill:#7B68EE,stroke:#5A4DB5,color:#fff
    style FD fill:#E67E22,stroke:#D35400,color:#fff
    style W1 fill:#50C878,stroke:#3A9A5C,color:#fff
    style W2 fill:#50C878,stroke:#3A9A5C,color:#fff
    style W3 fill:#50C878,stroke:#3A9A5C,color:#fff
    style HB fill:#50C878,stroke:#3A9A5C,color:#fff
    style TDB fill:#E74C3C,stroke:#C0392B,color:#fff
    style DLQ fill:#E74C3C,stroke:#C0392B,color:#fff
    style LOG fill:#E74C3C,stroke:#C0392B,color:#fff
    style MQ fill:#E67E22,stroke:#D35400,color:#fff
```

**Key Discussion Points:**
- Task state machine (scheduled → queued → running → completed/failed)
- At-least-once vs exactly-once execution guarantees
- Leader election for scheduler HA (Zookeeper/etcd)
- Delayed and recurring task scheduling (cron expression parsing)
- Worker heartbeat and task re-assignment on failure
- Dead letter queue for permanently failed tasks
- Priority-based scheduling and fairness
- Idempotent task execution
- Horizontal scaling of worker pools
- Task dependency DAG execution

---

## Quick Reference Matrix

### Fresher Level — Concept Focus

| # | System | Key Concepts | Difficulty |
|---|--------|-------------|------------|
| 1 | URL Shortener | Hashing, Cache, DB | ⭐ |
| 2 | Chat App | WebSocket, Queue | ⭐⭐ |
| 3 | File Storage | Chunking, Object Store | ⭐⭐ |
| 4 | Social Media | Feed, Graph, Media | ⭐⭐⭐ |
| 5 | Search Engine | Inverted Index, Ranking | ⭐⭐⭐ |
| 6 | E-Commerce | Cart, Payment, Inventory | ⭐⭐ |
| 7 | Ride Sharing | Geospatial, Matching | ⭐⭐⭐ |
| 8 | Video Streaming | Transcoding, CDN, HLS | ⭐⭐⭐ |
| 9 | Recommendation | CF, Content-Based, ML | ⭐⭐ |
| 10 | Food Delivery | 3-Sided Marketplace | ⭐⭐⭐ |
| 11 | Parking Lot | OOP, State Machine | ⭐ |
| 12 | Music Streaming | Audio Codec, Playlist | ⭐⭐ |
| 13 | Ticket Booking | Locking, Reservation | ⭐⭐ |
| 14 | Note Taking | Sync, Search, Offline | ⭐⭐ |
| 15 | Weather System | Time-Series, Pipeline | ⭐⭐ |
| 16 | Email Service | SMTP, IMAP, Spam | ⭐⭐ |
| 17 | File Sync | Delta Sync, Conflict | ⭐⭐⭐ |
| 18 | Calendar App | Recurring Events, TZ | ⭐⭐ |
| 19 | Quiz Platform | Timer, Grading | ⭐ |
| 20 | Auth System | JWT, OAuth, MFA | ⭐⭐ |

### Experienced Level — Scale Focus

| # | System | Key Scale Challenges | Difficulty |
|---|--------|---------------------|------------|
| 1 | bit.ly | Sharding, Analytics Pipeline | ⭐⭐⭐ |
| 2 | Redis | Consistent Hashing, Replication | ⭐⭐⭐⭐⭐ |
| 3 | Facebook | TAO, Fan-Out, Multi-Layer Cache | ⭐⭐⭐⭐⭐ |
| 4 | Netflix | ML Pipeline, Feature Store | ⭐⭐⭐⭐ |
| 5 | HDFS | Block Replication, NameNode HA | ⭐⭐⭐⭐ |
| 6 | WhatsApp | E2E Encryption, Millions WS | ⭐⭐⭐⭐ |
| 7 | Google Crawler | URL Frontier, Bloom Filter | ⭐⭐⭐⭐ |
| 8 | Memcached | Slab Allocator, Cache Stampede | ⭐⭐⭐ |
| 9 | Cloudflare CDN | Anycast, Edge Compute, WAF | ⭐⭐⭐⭐ |
| 10 | Google Search | Sharded Index, PageRank, ML Rank | ⭐⭐⭐⭐⭐ |
| 11 | Uber | H3 Cells, DISCO, Surge Pricing | ⭐⭐⭐⭐⭐ |
| 12 | YouTube | Transcoding Farm, Vitess, Ads | ⭐⭐⭐⭐⭐ |
| 13 | Zomato | 3-Sided Marketplace, RT Tracking | ⭐⭐⭐⭐ |
| 14 | Google Docs | OT/CRDT, Real-Time Collab | ⭐⭐⭐⭐⭐ |
| 15 | Amazon | Saga Pattern, Fraud Detection | ⭐⭐⭐⭐⭐ |
| 16 | Marketplace Recs | ANN Index, Feature Store, L2R | ⭐⭐⭐⭐ |
| 17 | Distributed DB | Raft/Paxos, LSM, 2PC | ⭐⭐⭐⭐⭐ |
| 18 | Twitter | Hybrid Fan-Out, Earlybird | ⭐⭐⭐⭐ |
| 19 | Instagram | Cassandra, Explore ML, Stories | ⭐⭐⭐⭐ |
| 20 | Task Scheduler | Leader Election, DLQ, DAG | ⭐⭐⭐⭐ |

---

## 🎯 Color Legend for All Diagrams

| Color | Meaning |
|-------|---------|
| 🔵 Blue (`#4A90D9`) | Users / Clients |
| 🟡 Orange (`#F5A623`) | Gateway / Load Balancer / Edge |
| 🟣 Purple (`#7B68EE`) | Core Services |
| 🟢 Green (`#50C878`) | Supporting Services / Processing |
| 🔴 Red (`#E74C3C`) | Persistent Storage / Databases |
| 🟠 Dark Orange (`#E67E22`) | Cache / Queues / Coordination |

---

> **💡 Tip:** For interview preparation, start with the Fresher level to build your fundamentals,
> then progressively tackle Experienced level questions. Focus on trade-offs, not just architecture.
