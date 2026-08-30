# Project Architecture Diagrams

---

## 1. ChargeIQ — Architecture

```text
                         ┌─────────────────────────────┐
                         │          USER / EV          │
                         │                             │
                         │  Current GPS Location       │
                         │  Vehicle Profile            │
                         │  Battery / Range            │
                         │  Charging Preferences       │
                         └──────────────┬──────────────┘
                                        │
                                        ▼
              ┌─────────────────────────────────────────────┐
              │              CHARGEIQ MOBILE APP            │
              │                  Flutter / Dart              │
              │                                             │
              │  ┌────────────┐      ┌────────────────────┐ │
              │  │ Home / Map │      │ Search / Filters   │ │
              │  └─────┬──────┘      └─────────┬──────────┘ │
              │        │                       │            │
              │  ┌─────▼──────┐      ┌─────────▼──────────┐ │
              │  │ Station    │      │ Vehicle Profile    │ │
              │  │ Details    │      │ Management         │ │
              │  └─────┬──────┘      └─────────┬──────────┘ │
              │        │                       │            │
              │  ┌─────▼───────────────────────▼──────────┐ │
              │  │       Application / Business Logic     │ │
              │  │                                        │ │
              │  │ • Station filtering                    │ │
              │  │ • Distance calculation                 │ │
              │  │ • AI scoring                            │ │
              │  │ • Quick Charge                         │ │
              │  │ • Route optimization                    │ │
              │  │ • Charging-stop selection              │ │
              │  └──────────────────┬─────────────────────┘ │
              └─────────────────────┼───────────────────────┘
                                    │
                 ┌──────────────────┼──────────────────┐
                 │                  │                  │
                 ▼                  ▼                  ▼
        ┌────────────────┐ ┌────────────────┐ ┌─────────────────┐
        │ Location / Map │ │ Charging       │ │ User / App      │
        │ Services       │ │ Station Data   │ │ Persistence     │
        │                │ │                │ │                 │
        │ GPS            │ │ Station        │ │ Account         │
        │ Maps           │ │ Availability   │ │ Vehicle profile │
        │ Places/Search  │ │ Rating         │ │ History         │
        │ Navigation     │ │ Connectors     │ │ Saved stations  │
        └───────┬────────┘ └───────┬────────┘ └───────┬─────────┘
                │                  │                  │
                └──────────────────┼──────────────────┘
                                   ▼
                    ┌──────────────────────────┐
                    │   STATION DATA / INPUTS  │
                    │                          │
                    │ Distance                 │
                    │ Availability             │
                    │ Rating                   │
                    │ Charging ports            │
                    │ Cost / charging factors  │
                    │ Vehicle compatibility    │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │   AI RECOMMENDATION      │
                    │       / SCORING          │
                    │                          │
                    │ Score candidate stations │
                    │ using multiple factors:  │
                    │                          │
                    │ Distance                 │
                    │ Availability             │
                    │ Rating                   │
                    │ Charging ports           │
                    │ User preferences         │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │ BEST CHARGING STATION    │
                    │                          │
                    │ Quick Charge             │
                    │ OR                       │
                    │ Optimized Route          │
                    └────────────┬─────────────┘
                                 │
                                 ▼
                    ┌──────────────────────────┐
                    │ MAP + NAVIGATION          │
                    │                          │
                    │ Route to selected station│
                    │ Charging stops            │
                    │ Turn-by-turn navigation  │
                    └──────────────────────────┘
```

**Interview one-liner:**
```text
User → Flutter App → Location/Station Data → Recommendation/Scoring → Map/Navigation
```

**Important points:**
- The report explicitly describes the AI recommendation as a **scoring mechanism** based on distance, rating, open status, and charging ports.
- Do **not** claim "we trained a machine-learning model" unless that's actually true. Describe it as a **multi-factor recommendation/scoring algorithm**, not ML.

---

## 2. Federated Learning — Architecture

```text
                         FEDERATED LEARNING SYSTEM
┌──────────────────────────────────────────────────────────────────────┐
│                                                                      │
│                         CENTRAL SERVER                               │
│                                                                      │
│              ┌──────────────────────────────────┐                    │
│              │        Flower Server             │                    │
│              │                                  │                    │
│              │       FedAvg Strategy            │                    │
│              │                                  │                    │
│              │  • 2 minimum clients             │                    │
│              │  • 100% client participation     │                    │
│              │  • 5 training rounds             │                    │
│              │  • aggregate model parameters    │                    │
│              └───────────────┬──────────────────┘                    │
│                              │                                       │
│                 Global Model Parameters                             │
│                              │                                       │
└──────────────────────────────┼───────────────────────────────────────┘
                               │
                 ┌─────────────┴─────────────┐
                 │                           │
                 ▼                           ▼
       ┌───────────────────┐       ┌───────────────────┐
       │     CLIENT 0      │       │     CLIENT 1      │
       │                   │       │                   │
       │ Local Corpus      │       │ Local Corpus      │
       │       │           │       │       │           │
       │       ▼           │       │       ▼           │
       │ DataHandler       │       │ DataHandler       │
       │       │           │       │       │           │
       │       ▼           │       │       ▼           │
       │ n-gram creation   │       │ n-gram creation   │
       │ padding           │       │ padding           │
       │ X / y creation    │       │ X / y creation    │
       │       │           │       │       │           │
       │       ▼           │       │       ▼           │
       │ Shared Tokenizer  │       │ Shared Tokenizer  │
       │ Shared Vocabulary │       │ Shared Vocabulary │
       │ Shared Max Length │       │ Shared Max Length │
       │       │           │       │       │           │
       │       ▼           │       │       ▼           │
       │ LSTM Model        │       │ LSTM Model        │
       │                   │       │                   │
       │ Embedding         │       │ Embedding         │
       │ LSTM              │       │ LSTM              │
       │ Dropout           │       │ Dropout           │
       │ LSTM              │       │ LSTM              │
       │ Dropout           │       │ Dropout           │
       │ Softmax           │       │ Softmax           │
       │       │           │       │       │           │
       │       ▼           │       │       ▼           │
       │ Local Training    │       │ Local Training    │
       │       │           │       │       │           │
       │       ▼           │       │       ▼           │
       │ Device Check      │       │ Device Check      │
       │ CPU/RAM/Battery   │       │ CPU/RAM/Battery   │
       │ Network           │       │ Network           │
       │       │           │       │       │           │
       │       ▼           │       │       ▼           │
       │ Model Parameters  │       │ Model Parameters  │
       └────────┬──────────┘       └────────┬──────────┘
                │                           │
                │ encrypted parameters      │ encrypted parameters
                └─────────────┬─────────────┘
                              ▼
                       Flower Server
                              │
                              ▼
                         FedAvg
                              │
                              ▼
                      Global Model
                              │
                    ┌─────────┴─────────┐
                    ▼                   ▼
                 Client 0            Client 1
```

### Training loop (per round)

```text
ROUND 1
   │
   ▼
Server initializes global LSTM model
   │
   ▼
Send global model parameters
   │
   ├──────────────► Client 0
   │                    │
   │                    ▼
   │              Train on local data
   │                    │
   │                    ▼
   │              Local weights W0
   │
   └──────────────► Client 1
                        │
                        ▼
                  Train on local data
                        │
                        ▼
                  Local weights W1
                        │
                        └──────────────┐
                                       ▼
                              ┌─────────────────┐
                              │ Server          │
                              │                 │
                              │ FedAvg(W0, W1)  │
                              └────────┬────────┘
                                       │
                                       ▼
                              New Global Model
                                       │
                                       ▼
                                  ROUND 2
                                       │
                                      ...
                                       │
                                       ▼
                                  ROUND 5
                                       │
                                       ▼
                              Final Global Model
```

### Inside each client

```text
                LOCAL CLIENT
                     │
                     ▼
              Local Text Corpus
                     │
                     ▼
             Shared Tokenizer
                     │
                     ▼
              Token Sequences
                     │
                     ▼
               N-Gram Creation
                     │
                     ▼
                Padding
                     │
                     ▼
                X + y
                     │
                     ▼
             ┌───────────────┐
             │ LSTM Network  │
             │               │
             │ Embedding     │
             │ LSTM          │
             │ Dropout       │
             │ LSTM          │
             │ Dropout       │
             │ Softmax       │
             └───────┬───────┘
                     │
                     ▼
               Device Check
           ┌─────────┼─────────┐
           ▼         ▼         ▼
         CPU       RAM      Battery
                     +
                  Network
                     │
                     ▼
               Local Training
                     │
                     ▼
               Model Weights
                     │
                     ▼
                Encryption
                     │
                     ▼
              Send to Server
```

**Interview one-liner:**
```text
Local Data → Local LSTM Training → Model Updates → Flower Server → FedAvg → Global Model → Clients
```

**Important points:**
- The server's `FedAvg` strategy requires a minimum of 2 clients and is configured for 100% client participation across 5 training rounds.
- Each client preprocesses its own corpus using a **shared** tokenizer, vocabulary, and max sequence length — this is what keeps the clients' inputs consistent despite training on different local data.
- LSTM architecture: **Embedding → LSTM → Dropout → LSTM → Dropout → Dense/Softmax**.
- Core idea to articulate: train collaboratively without centralizing raw client data.

---

## 3. CacheSphere — Architecture

```text
                         USER
                          │
                          ▼
              ┌──────────────────────┐
              │ Browser              │
              │                      │
              │ CacheSphere UI       │
              │ localhost:5173       │
              │ / deployed frontend  │
              └──────────┬───────────┘
                         │
                    HTTP / JSON
                         │
                         ▼
              ┌──────────────────────┐
              │ React + TypeScript   │
              │ Dashboard            │
              │                      │
              │ ┌──────────────────┐ │
              │ │ SettingsPanel    │ │
              │ ├──────────────────┤ │
              │ │ StatsPanel       │ │
              │ ├──────────────────┤ │
              │ │ CacheVisualization││
              │ ├──────────────────┤ │
              │ │ ComparisonView   │ │
              │ └──────────────────┘ │
              └──────────┬───────────┘
                         │
                    REST API
                    JSON/HTTP
                         │
                         ▼
              ┌──────────────────────┐
              │ Go REST API Server   │
              │                      │
              │ POST /start          │
              │ POST /request        │
              │ GET  /cache          │
              │ GET  /stats          │
              │ POST /reset          │
              │ POST /changePolicy   │
              │ POST /changeCacheSize│
              │ POST /compare        │
              └──────────┬───────────┘
                         │
                         ▼
              ┌──────────────────────┐
              │ Cache Engine         │
              │ internal/cache       │
              │                      │
              │ Mutex Protected      │
              └──────────┬───────────┘
                         │
              ┌──────────┼──────────┐
              │          │          │
              ▼          ▼          ▼
        ┌──────────┐ ┌──────────┐ ┌──────────┐
        │   LRU    │ │   LFU    │ │   FIFO   │
        │          │ │          │ │          │
        │ HashMap  │ │ HashMap  │ │ HashMap  │
        │    +     │ │    +     │ │    +     │
        │ Doubly   │ │Frequency │ │Insertion │
        │ Linked   │ │ Buckets  │ │  Queue   │
        │  List    │ │    +     │ │          │
        │          │ │ MinFreq  │ │          │
        └──────────┘ └──────────┘ └──────────┘
              │          │          │
              └──────────┼──────────┘
                         │
                         ▼
                Cache State + Stats
                         │
                         ▼
                    Go REST API
                         │
                         ▼
                  React Dashboard
```

### Request flow (`GET /request?key=A`)

```text
User
 │
 ▼
React UI
 │
 │ HTTP request
 ▼
Go API
 │
 ▼
Cache Engine
 │
 ├── Is A present?
 │
 ├── YES ──► CACHE HIT
 │             │
 │             └── update policy metadata
 │
 │
 └── NO ───► CACHE MISS
               │
               ▼
          Add A to cache
               │
               ▼
        Cache capacity exceeded?
               │
          ┌────┴────┐
          │         │
         NO        YES
          │         │
          │         ▼
          │    Eviction Policy
          │         │
          │    ┌────┼────┐
          │    ▼    ▼    ▼
          │   LRU   LFU  FIFO
          │         │
          └────┬────┘
               ▼
          Update Stats
               │
               ▼
          Return JSON
               │
               ▼
          React Dashboard
```

### Eviction algorithms

**LRU**
```text
HashMap
   +
Doubly Linked List

GET/PUT → O(1)
```
The hash map finds the item quickly, while the linked list maintains recency order.

**LFU**
```text
HashMap
   +
Frequency Buckets
   +
MinFreq
```
Tracks access frequency per item and evicts the least frequently used one. Documented as **O(1) amortized**.

**FIFO**
```text
HashMap
   +
Insertion-Order Queue

O(1)
```
Evicts the item that entered the cache first.

### Comparison flow (`/compare`)

```text
                   Same Request Sequence
                           │
             ┌─────────────┼─────────────┐
             ▼             ▼             ▼
           LRU            LFU           FIFO
             │             │             │
             ▼             ▼             ▼
          Hits/Miss     Hits/Miss     Hits/Miss
          Latency       Latency       Latency
             │             │             │
             └─────────────┼─────────────┘
                           ▼
                   Comparison Results
                           │
                           ▼
                    React Dashboard
```

**Interview one-liner:**
```text
React UI → Go REST API → Cache Engine → LRU/LFU/FIFO → Statistics → React UI
```

**Important points:**
- CacheSphere is **not** a production CDN — it's a cache eviction simulator. It makes no real network calls, doesn't talk to an origin server, doesn't persist data, and doesn't manage multiple users.
- `/compare` runs LRU, LFU, and FIFO against the **same generated request sequence** without changing the active simulator state — a strong point to raise since it demonstrates fair algorithm comparison.

---

## Summary Table

| Project | Core technical idea |
|---|---|
| **ChargeIQ** | Multi-factor EV station recommendation + route optimization |
| **Federated Learning** | Train collaboratively without centralizing raw client data |
| **CacheSphere** | Simulate and compare cache eviction strategies |

**Interview note:** Federated Learning and CacheSphere support the deepest follow-up questions (communication flow, data flow, algorithms, complexity, concurrency, aggregation, privacy, failure cases, design trade-offs). ChargeIQ questions will more likely center on API integration, recommendation logic, location services, route optimization, and overall app architecture.
