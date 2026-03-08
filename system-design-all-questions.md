# System Design Interview Questions

---

## 🟢 Easy (15 Problems)

# Designing a Simple URL Shortening Service: A TinyURL Approach

**Difficulty:** Easy

## Problem Statement
Design a URL shortening service like TinyURL that converts long URLs into short, unique aliases and redirects users to the original URL.

---

## Functional Requirements
- Shorten a long URL to a unique short URL
- Redirect short URL to original long URL
- Custom aliases (optional)
- URL expiry (optional)
- Analytics: click counts (optional)

## Non-Functional Requirements
- High availability (99.99%)
- Low latency redirects (<10ms)
- Scalable to billions of URLs

---

## Capacity Estimation

| Metric | Value |
|--------|-------|
| Write QPS | 1,000/sec |
| Read QPS | 100,000/sec |
| URLs stored (10 yr) | ~36.5B |
| Storage per URL | ~500 bytes |
| Total storage | ~18 TB |

---

## API Design

```
POST /shorten
  Body: { "long_url": "...", "custom_alias": "...", "expiry_days": 30 }
  Response: { "short_url": "https://tinyurl.com/abc123" }

GET /{short_code}
  Response: 301/302 Redirect

DELETE /{short_code}
```

---

## Architecture

```
Client → Load Balancer
  Write Path → URL Shortener Service → PostgreSQL
                    ↓
               Base62 ID Generator

  Read Path  → Redirect Service → Redis Cache → PostgreSQL
                    ↓
               CDN (edge redirect)
```

---

## Short Code Generation

- **Base62 encoding** of auto-incremented ID: [a-z, A-Z, 0-9]
- 7 characters → 62^7 ≈ 3.5 trillion unique URLs
- Preferred: Distributed ID (Snowflake) → encode to Base62

---

## Database Schema

```sql
CREATE TABLE urls (
  short_code   VARCHAR(10)  PRIMARY KEY,
  long_url     TEXT         NOT NULL,
  user_id      BIGINT,
  created_at   TIMESTAMP,
  expires_at   TIMESTAMP,
  click_count  BIGINT DEFAULT 0
);
```

---

## Read Flow
1. Check Redis cache for short_code
2. Cache hit → return 302 redirect
3. Cache miss → query DB → populate cache → redirect

## Write Flow
1. Check if URL exists (dedup)
2. Generate unique short code
3. Store in DB → return short URL

---

## Redirect Strategy
- **301**: Permanent, browser caches → less server load
- **302**: Temporary, server processes every request → better analytics
- Use 302 if click tracking is needed

---

## Scalability

| Concern | Solution |
|---------|----------|
| Hot URLs | Redis LRU cache |
| DB writes | Shard by short_code hash |
| ID uniqueness | ZooKeeper-coordinated counter ranges |
| Global | CDN + multi-region |

---

## Trade-offs

| Decision | Option A | Option B |
|----------|----------|----------|
| Redirect | 301 (less load) | 302 (analytics) |
| ID generation | Hash-based | Counter + Base62 |
| Storage | SQL (ACID) | NoSQL (scale) |

---

## Summary
TinyURL is a read-heavy system. The key pillars: Base62 + distributed ID for short codes, Redis caching, and clean redirect flow. Horizontal scaling of stateless redirect servers handles traffic spikes.

---

# Design an Efficient Parking Lot System

**Difficulty:** Easy

## Problem Statement
Design a parking lot system that manages vehicle entry/exit, slot allocation, fee calculation, and real-time availability.

---

## Functional Requirements
- Track slots by type (small, medium, large)
- Assign nearest available slot on entry
- Calculate fee on exit (time-based)
- Real-time slot availability display
- Support multiple entry/exit gates

## Non-Functional Requirements
- Real-time updates
- High availability for gate operations
- No double-booking

---

## OOP Design

```java
enum VehicleType { MOTORCYCLE, CAR, TRUCK }
enum SlotStatus  { AVAILABLE, OCCUPIED }

class Vehicle { String licensePlate; VehicleType type; }

class ParkingSlot {
    String slotId; VehicleType compatibleType;
    SlotStatus status; int floor; int position;
}

class Ticket {
    String ticketId; Vehicle vehicle; ParkingSlot slot;
    LocalDateTime entryTime; double fee;
}
```

---

## Architecture

```
Gate Terminal
    ↓
API Gateway
    ↓
Slot Management Service → Redis (live slots) + PostgreSQL (tickets)
Fee Service             → PostgreSQL
Display Service         → WebSocket / SSE to boards
```

---

## Slot Allocation
- Min-heap per vehicle type, ordered by (floor, position)
- On entry: poll heap, mark OCCUPIED in Redis (atomic SETNX)
- On exit: push slot back, mark AVAILABLE

---

## Fee Calculation

```
fee = hourlyRate[vehicleType] × ceil(duration_hours)
```

| Vehicle | Rate/Hour |
|---------|-----------|
| Motorcycle | $2 |
| Car | $5 |
| Truck | $10 |

---

## Database Schema

```sql
CREATE TABLE tickets (
  ticket_id    UUID PRIMARY KEY,
  license_plate VARCHAR(20),
  slot_id       VARCHAR(20),
  entry_time    TIMESTAMP,
  exit_time     TIMESTAMP,
  fee           DECIMAL(8,2)
);
```

---

## Concurrency
- Redis `SETNX` atomically locks slot during assignment
- Prevents race at multiple entry gates simultaneously

---

## Summary
Parking lot combines OOP slot model with Redis-backed real-time state. Min-heap ensures nearest-slot assignment; atomic Redis ops prevent double-booking.

---

# Design a Fitness Tracking App

**Difficulty:** Easy

## Problem Statement
Design a fitness tracking app that records workouts, tracks health metrics (steps, calories, heart rate), sets goals, and provides analytics.

---

## Functional Requirements
- Record workouts (type, duration, calories)
- Track daily metrics: steps, calories, distance, heart rate
- Set and monitor fitness goals
- View historical trends
- Sync with wearable devices
- Social features: friends, leaderboards

## Non-Functional Requirements
- Handle millions of users with continuous metric ingestion
- Low-latency dashboard reads
- Efficient time-series storage
- Offline mobile sync

---

## Capacity Estimation

| Metric | Value |
|--------|-------|
| Active users | 50M |
| Events/user/day | ~1,000 |
| Total events/day | 50B |
| Daily storage | ~2.5 TB |

---

## API Design

```
POST /workouts
  Body: { "type": "running", "duration_min": 30, "calories": 300 }

GET  /workouts?user_id=&date_range=
POST /metrics/ingest    (batch from wearable)
GET  /metrics/daily?user_id=&date=
POST /goals
GET  /leaderboard?type=steps&period=weekly
```

---

## Architecture

```
Mobile/Wearable
    ↓
API Gateway
    ↓
Workout Service → PostgreSQL (workouts, goals)
Metrics Ingest  → Kafka → InfluxDB/TimescaleDB
Analytics       → Redis (dashboard cache)
```

---

## Data Models

### Workouts (PostgreSQL)
```sql
CREATE TABLE workouts (
  workout_id UUID PRIMARY KEY, user_id BIGINT,
  type VARCHAR(50), started_at TIMESTAMP,
  duration_min INT, calories INT, distance_km FLOAT
);
```

### Metrics (TimescaleDB time-series)
```
measurement: heart_rate | tags: user_id | fields: bpm | time: ...
measurement: steps       | tags: user_id | fields: count | time: ...
```

---

## Key Design Decisions

### Ingestion Pipeline
1. Wearable sends batch metrics every 60s
2. API pushes to Kafka
3. Stream processors aggregate per-minute/hourly
4. Stored in TimescaleDB with retention policy

### Dashboard
- Pre-aggregate daily summaries; cache in Redis (TTL 5 min)
- Avoids expensive time-series scans per page load

### Offline Sync
- Mobile stores events in local SQLite
- On reconnect: sends delta batch with idempotency keys

---

## Trade-offs

| Concern | Decision |
|---------|----------|
| Metric store | TimescaleDB (SQL + time-series) |
| Ingestion | Kafka (async, decoupled) |
| Leaderboard | Redis sorted sets |
| Data retention | 90d full resolution; 1yr daily aggregates |

---

## Summary
A write-heavy, time-series system. Kafka decouples ingestion, TimescaleDB handles efficient queries, Redis caches dashboards. Pre-computed daily summaries are the key to fast reads.

---

# Design a Weather Reporting System

**Difficulty:** Easy

## Problem Statement
Collect data from weather stations, process it, and serve current conditions and forecasts to millions of users.

---

## Functional Requirements
- Ingest data from stations (temp, humidity, wind, pressure)
- Serve current weather by location
- Provide hourly/daily forecasts
- Weather alerts for severe conditions
- Historical data queries

## Non-Functional Requirements
- High read throughput (millions of queries/sec)
- Low ingestion latency (<1 min from station)
- High availability

---

## Capacity Estimation

| Metric | Value |
|--------|-------|
| Weather stations | 100,000 globally |
| Reports/station/min | 1 |
| Total ingestion | ~1,700 events/sec |
| Read QPS | 1M/sec peak |

---

## Architecture

```
Stations/Satellites/3rd Party APIs
    ↓ (MQTT)
Ingestion Gateway → Kafka
    ↓
Stream Processor (Flink)
    ├── Current → Redis (geo-cell indexed)
    ├── Aggregates → TimescaleDB
    └── Alerts → Alert Service → Push Notifications
    ↓
Read API → CDN/Edge Cache → Clients
```

---

## Data Models

### Station Report (Kafka message)
```json
{
  "station_id": "WS-NY-001", "lat": 40.71, "lng": -74.00,
  "timestamp": "...", "temp_c": 22.5, "humidity_pct": 65,
  "wind_kph": 15, "pressure_hpa": 1013
}
```

### Current Conditions (Redis)
- Key: `weather:geo:{geohash_5chars}` | TTL: 10 minutes

### Historical (TimescaleDB)
```sql
CREATE TABLE weather_readings (
  station_id VARCHAR(20), time TIMESTAMPTZ,
  temp_c FLOAT, humidity FLOAT, wind_kph FLOAT
);
SELECT create_hypertable('weather_readings', 'time');
```

---

## Geo-Spatial Lookup
- Index stations with Geohash (precision 5 = ~5km cells)
- Query: user lat/lng → geohash → Redis lookup
- Fallback: nearest neighbor in adjacent cells

---

## Forecast Generation
- Scheduled ML model runs every 6 hours
- Inputs: historical + current + satellite
- Pre-computed per city stored in PostgreSQL

---

## Summary
MQTT + Kafka decouple ingestion. Geohash-indexed Redis enables sub-millisecond current lookups. Pre-computed forecasts + CDN cache handle millions of users.

---

# Design Pastebin

**Difficulty:** Easy

## Problem Statement
Design Pastebin — a service for storing and sharing plain text snippets via unique URLs.

---

## Functional Requirements
- Upload text, receive unique URL
- Read content via URL
- Optional: expiry, password protection, syntax highlighting
- User accounts with paste history
- View count analytics

## Non-Functional Requirements
- Highly available reads
- Durability (no lost pastes)
- Low latency (<50ms reads)
- Support up to 10MB per paste

---

## Capacity Estimation

| Metric | Value |
|--------|-------|
| New pastes/day | 1M |
| Reads/day | 100M (100:1) |
| Avg paste size | 10 KB |
| 10-year storage | ~36 TB |

---

## Architecture

```
Client → Load Balancer → API Servers
  Write: ID Generator → PostgreSQL (metadata) + S3 (content)
  Read:  Redis Cache → S3 (content) + PostgreSQL (metadata)
```

---

## Paste ID Generation
- Base62 encode counter: 7 chars → 62^7 ≈ 3.5T pastes
- Or: UUID → encode first 8 bytes to Base62

---

## Database Schema

```sql
CREATE TABLE pastes (
  paste_id      VARCHAR(10) PRIMARY KEY,
  user_id       BIGINT,
  s3_key        VARCHAR(200),
  syntax        VARCHAR(30),
  password_hash VARCHAR(64),
  created_at    TIMESTAMP,
  expires_at    TIMESTAMP,
  view_count    BIGINT DEFAULT 0
);
```

Content in S3: `pastes/{paste_id}.txt`

---

## Read/Write Flow

**Write:**
1. Generate paste_id
2. Upload content to S3
3. Insert metadata to PostgreSQL
4. Return URL

**Read:**
1. Check Redis: `paste:{paste_id}`
2. HIT → return content
3. MISS → PostgreSQL metadata + S3 fetch → cache (TTL = expiry or 1hr)

---

## Features

- **Expiry**: cron job deletes expired entries from DB + S3
- **Password**: bcrypt hash stored; validate `X-Password` header on read
- **View count**: async Kafka batch update (performance over exactness)

---

## Trade-offs

| Concern | Decision |
|---------|----------|
| Content storage | S3 (cheap, durable) — never DB BLOBs at scale |
| Paste ID | Base62 counter (predictable) vs random (privacy) |
| View count | Async batch (performance) |

---

## Summary
Separating metadata (PostgreSQL) from content (S3) is the core design decision. Redis caching handles heavy read traffic. Simple but solid system design fundamentals.

---

# Design a Nested Comments System

**Difficulty:** Easy

## Problem Statement
Design a threaded comments system (like Reddit/HackerNews) supporting hierarchical replies, voting, and efficient rendering.

---

## Functional Requirements
- Post comments on content
- Reply to any comment (arbitrary depth)
- Upvote/downvote comments
- Sort by: newest, top (score), controversial
- Edit and delete comments
- Paginate top-level comments

## Non-Functional Requirements
- Support millions of comments per viral post
- Fast reads
- Deleted comments preserved as [deleted] to maintain thread structure

---

## Tree Representation Strategies

| Strategy | Pros | Cons |
|----------|------|------|
| Adjacency List (parent_id) | Simple writes | Recursive queries (slow) |
| Materialized Path (ltree) | Fast subtree queries | Path updates on move |
| Closure Table | Flexible | Large storage |

**Recommended**: PostgreSQL `ltree` Materialized Path

---

## Data Model

```sql
CREATE TABLE comments (
  comment_id  BIGSERIAL PRIMARY KEY,
  content_id  VARCHAR(50) NOT NULL,
  parent_id   BIGINT REFERENCES comments,
  author_id   BIGINT NOT NULL,
  text        TEXT,
  score       INT DEFAULT 0,
  depth       INT DEFAULT 0,
  path        LTREE,                 -- e.g. 1.4.17.92
  created_at  TIMESTAMP,
  is_deleted  BOOLEAN DEFAULT FALSE
);

CREATE INDEX idx_content_path ON comments (content_id, path);

CREATE TABLE comment_votes (
  user_id    BIGINT, comment_id BIGINT,
  vote       SMALLINT,   -- 1 or -1
  PRIMARY KEY (user_id, comment_id)
);
```

---

## ltree Queries

```sql
-- All descendants of comment 1.4.17
SELECT * FROM comments WHERE path <@ '1.4.17';

-- Direct children
SELECT * FROM comments WHERE path ~ '1.4.17.*{1}';
```

---

## Architecture

```
Client → API Gateway
    ↓
Comment Service → PostgreSQL (comments, votes)
    ↓            Redis (score cache)
Fan-out → Kafka → Notification Service (reply alerts)
```

---

## Loading Strategy

1. Load top-level comments (depth=0) by score, 20/page
2. Eager-load first 3 replies per top comment
3. "Show more replies" → lazy-load deeper
4. Avoids loading thousands of nested comments upfront

---

## Deletion Strategy
- Soft delete: `is_deleted = TRUE`, text → `[deleted]`
- Preserves thread continuity for child comments
- Hard delete only when no children exist

---

## Trade-offs

| Concern | Decision |
|---------|----------|
| Tree storage | ltree (SQL-native, powerful) |
| Vote consistency | Eventual (Redis first, DB sync) |
| Load strategy | Lazy-load deep threads |

---

## Summary
ltree makes subtree queries elegant. Pagination + lazy-loading keeps initial load fast even for huge threads. Soft deletion preserves thread structure.

---

# Design an Online Presence Indicator Service

**Difficulty:** Easy

## Problem Statement
Design an online presence service (like WhatsApp/Slack's green dot) showing real-time user online/offline/away status.

---

## Functional Requirements
- Show online/offline/away status
- Real-time updates on connect/disconnect
- Query presence for multiple user IDs
- Last seen timestamp for offline users
- Privacy controls

## Non-Functional Requirements
- <1 second status propagation
- Scale to 500M+ users, millions online simultaneously
- Fault tolerant (crashes → no ghost presences)

---

## Core Mechanism: Heartbeat + TTL

```
Client → WebSocket → Server
  Client sends heartbeat every 30s
  Server: SET presence:{user_id} "online" EX 60

If no heartbeat → TTL expires → user goes "offline"
This handles crashes (not just clean disconnects)
```

---

## Architecture

```
Client (WebSocket)
    ↓
WebSocket Gateway (sticky sessions)
    ├── Heartbeat → Redis (TTL-based presence)
    ├── Presence Query API → Redis MGET
    └── Status Changes → Kafka → Fan-out → WebSocket push to friends
```

---

## Presence Query

```
GET /presence?user_ids=u1,u2,u3

Redis MGET: [presence:u1, presence:u2, presence:u3]
→ ["online", null, "away"]   (null = offline)
```

Single `MGET` for batch lookup. O(n), extremely fast.

---

## Status Types

| Status | Condition |
|--------|-----------|
| Online | Active heartbeat within 60s |
| Away | Heartbeat active but no interaction >5 min |
| Offline | TTL expired or explicit disconnect |
| Hidden | User opted out; shows as offline |

---

## Fan-out Challenge

When user A comes online, friends see green dot:
1. Status change → Kafka
2. Fan-out service queries A's friend list
3. Pushes WebSocket message to each online friend's server

**Scale problem**: users with 10K+ friends → high fan-out cost

Solutions:
- Subscribe-on-demand: only push to friends viewing A's chat
- Pull model: friends check Redis on demand (slight delay)

---

## Privacy Controls

```
presence_privacy:{user_id} → "everyone" | "contacts_only" | "nobody"
```
Presence query API enforces per-requester privacy.

---

## Trade-offs

| Concern | Decision |
|---------|----------|
| Disconnect detection | Heartbeat TTL (handles crashes) |
| Fan-out | Push (real-time) vs Pull (scalable) |
| Last seen | Redis (fast) vs DB (durable) |

---

## Summary
Presence = heartbeat + TTL. Redis key expiry handles both graceful and crash disconnects. Fan-out to large friend lists is the main scaling challenge — subscribe-on-demand model solves it.

---

# Design a Vending Machine System

**Difficulty:** Easy

## Problem Statement
Design a vending machine — both the software state machine for a single unit and backend for fleet management.

---

## State Machine

```
IDLE → ITEM_SELECTED → PAYMENT_IN_PROGRESS → DISPENSING → IDLE
                            ↓
                       REFUNDING (cancel or failure)

Transitions:
IDLE + selectItem()       → ITEM_SELECTED
ITEM_SELECTED + insert()  → PAYMENT_IN_PROGRESS
PAYMENT_IN_PROGRESS + [amt >= price] → DISPENSING
PAYMENT_IN_PROGRESS + cancel()       → REFUNDING → IDLE
DISPENSING + done()       → IDLE (return change)
```

---

## OOP Design

```java
interface PaymentStrategy {
    boolean processPayment(double amount);
}
class CoinPayment implements PaymentStrategy { ... }
class CardPayment  implements PaymentStrategy { ... }

class VendingMachine {
    Map<String, Product> inventory;
    VendingState currentState;
    double insertedAmount;
    PaymentStrategy paymentStrategy;

    void selectProduct(String id);
    void insertPayment(double amount);
    void cancel();
    double returnChange();
}
```

---

## Fleet Management Architecture

```
Vending Machines (IoT) → MQTT over 4G/WiFi
    ↓
IoT Gateway / MQTT Broker → Kafka
    ↓
Inventory Service  → PostgreSQL
Analytics Service  → ClickHouse
Admin Dashboard    → React
```

---

## Database Schema

```sql
CREATE TABLE machine_inventory (
  machine_id VARCHAR(20), product_id VARCHAR(20),
  slot_id VARCHAR(5), quantity INT, price DECIMAL(6,2),
  PRIMARY KEY (machine_id, slot_id)
);

CREATE TABLE sales_events (
  event_id UUID PRIMARY KEY, machine_id VARCHAR(20),
  product_id VARCHAR(20), amount_paid DECIMAL(6,2),
  payment_type VARCHAR(20), sold_at TIMESTAMP
);
```

---

## Telemetry Message (MQTT)

```json
{
  "machine_id": "VM-NYC-001",
  "inventory": [{"slot": "A1", "qty": 5}],
  "status": "operational",
  "total_sales_today": 120.50
}
```

---

## Key Features

- **Change**: dispense greedily (quarters → dimes → nickels → pennies)
- **Low stock alerts**: qty <= 2 → Kafka event → SMS to technician
- **Remote price update**: MQTT retained message → machine updates on subscribe
- **Offline operation**: machine runs standalone, syncs on reconnect

---

## Trade-offs

| Concern | Decision |
|---------|----------|
| Connectivity loss | Standalone operation + sync on reconnect |
| Payment failure | Timeout → REFUNDING state |
| Concurrency | Single-threaded state machine (no concurrency issue) |

---

## Summary
Vending machine = classic state machine at unit level. At fleet scale, MQTT + Kafka enables reliable IoT telemetry. Critical insight: machines must operate autonomously offline.

---

# Design a Tagging Service

**Difficulty:** Easy

## Problem Statement
Design a generic tagging service for associating tags with any entity type (posts, products, videos), with search, autocomplete, and trending.

---

## Functional Requirements
- Add/remove tags from any entity
- Get all tags for an entity
- Get all entities with a given tag
- Tag autocomplete/suggestions
- Tag normalization (lowercase, trim)
- Tag popularity / trending

## Non-Functional Requirements
- Generic across entity types
- Fast tag lookup and reverse lookup
- Low-latency autocomplete (<50ms)

---

## API Design

```
POST /tags
  Body: { "entity_id": "post-123", "entity_type": "post", "tags": ["java", "spring"] }

GET  /tags?entity_id=post-123&entity_type=post
GET  /entities?tag=java&entity_type=post&page=1
GET  /tags/autocomplete?prefix=spr
GET  /tags/trending?period=24h
DELETE /tags
  Body: { "entity_id": "post-123", "entity_type": "post", "tags": ["java"] }
```

---

## Data Model

```sql
CREATE TABLE entity_tags (
  entity_type  VARCHAR(50),
  entity_id    VARCHAR(100),
  tag          VARCHAR(100),
  created_at   TIMESTAMP,
  PRIMARY KEY (entity_type, entity_id, tag)
);
CREATE INDEX idx_entity ON entity_tags (entity_type, entity_id);
CREATE INDEX idx_tag    ON entity_tags (entity_type, tag);

CREATE TABLE tags (
  tag          VARCHAR(100) PRIMARY KEY,
  usage_count  BIGINT DEFAULT 0
);
```

---

## Autocomplete with Redis Sorted Sets

```
ZADD tags:autocomplete 0 "spring"
ZADD tags:autocomplete 0 "springboot"

ZRANGEBYLEX tags:autocomplete "[spr" "[sprÿ" LIMIT 0 10
→ ["spring", "springboot", "sprint"]
```

Prefix search in O(log n + m). Add tags to sorted set on creation.

---

## Trending Tags

```redis
ZINCRBY trending:tags:24h java 1
ZREVRANGE trending:tags:24h 0 9 WITHSCORES   → top 10
```

Reset via time-windowed keys: `trending:tags:{hour_bucket}`

---

## Tag Normalization

```
"  Java  " → "java"
Rules: lowercase, trim, replace spaces with hyphens, max 50 chars
```

---

## Architecture

```
Client → API Gateway
    ↓
Tag Write Service → PostgreSQL + Kafka (tag events)
Tag Read Service  → PostgreSQL / Redis
Autocomplete      → Redis Sorted Set
Trending          → Kafka consumer → Redis
```

---

## Trade-offs

| Concern | Decision |
|---------|----------|
| Storage | Relational (queryable joins) |
| Autocomplete | Redis sorted set (simple, fast) |
| Trending | Redis sliding window (approximate) |

---

## Summary
Tagging is a many-to-many join problem. PostgreSQL handles relational mapping; Redis powers autocomplete and trending. Generic `entity_type` column makes it reusable across domains.

---

# Design Craigslist

**Difficulty:** Easy

## Problem Statement
Design a classifieds platform where users post and browse local listings by location and category.

---

## Functional Requirements
- Post listings (title, description, category, price, location, images)
- Browse by location + category
- Search by keyword
- Contact poster via anonymous email relay
- Listing expiry (30 days)
- Flag listings as spam

## Non-Functional Requirements
- Read-heavy (browsing-dominant)
- Geographic locality
- Anti-spam protection

---

## Capacity Estimation

| Metric | Value |
|--------|-------|
| Active listings | 100M |
| New listings/day | 1M |
| Reads/day | 1B |
| Image storage | ~200 TB |

---

## API Design

```
POST /listings
  Body: { "title": "...", "category": "furniture", "price": 200,
          "location": { "city": "NYC", "zip": "10001" } }

GET  /listings?city=NYC&category=furniture&page=1
GET  /listings/{listing_id}
GET  /listings/search?q=sofa&city=NYC
POST /listings/{listing_id}/contact
POST /listings/{listing_id}/flag
```

---

## Architecture

```
Client → CDN (images) → API Gateway
    ↓
Listing Service → PostgreSQL + Elasticsearch (search)
Image Service   → S3
Email Relay     → SendGrid (anonymous contact)
Moderation      → Kafka → ML Classifier
```

---

## Database Schema

```sql
CREATE TABLE listings (
  listing_id   UUID PRIMARY KEY,
  user_id      BIGINT,
  title        VARCHAR(200),
  category     VARCHAR(50),
  price        DECIMAL(10,2),
  city         VARCHAR(100), zip_code VARCHAR(10),
  lat FLOAT, lng FLOAT,
  status       VARCHAR(20) DEFAULT 'active',
  post_date    TIMESTAMP, expiry_date TIMESTAMP,
  image_urls   TEXT[],
  email_hash   VARCHAR(64)   -- hashed poster email
);
CREATE INDEX idx_city_category ON listings (city, category, post_date DESC);
```

---

## Key Features

### Anonymous Email Relay
- Poster never reveals real email
- Craigslist alias: `reply-xxxx@craigslist.org`
- Inbound → relay service → lookup real email → forward
- Prevents spam harvesting

### Search
- Elasticsearch: full-text on title + description
- Filters: city, category, price range, date
- Sync via Kafka CDC from PostgreSQL

### Anti-Spam
- Rate limit: max 10 posts/day per account
- Phone verification for sensitive categories
- ML classifier on content
- Auto-remove at N community flags threshold

### Listing Expiry
- Cron: `UPDATE listings SET status='expired' WHERE expiry_date < NOW()`
- Email reminder before expiry

---

## Trade-offs

| Concern | Decision |
|---------|----------|
| Search | Elasticsearch (full-text) |
| Images | S3 (never DB blobs) |
| Email privacy | Relay service (essential) |

---

## Summary
Craigslist is a geo-partitioned classifieds platform. City + category indexing handles browsing; Elasticsearch handles search. Anonymous email relay is the critical privacy feature.

---

# Design a Multi-Device Screenshot Capture System

**Difficulty:** Easy

## Problem Statement
Design a system that captures screenshots of web pages across multiple devices/browsers and stores/serves them reliably.

---

## Functional Requirements
- Trigger screenshot via API
- Capture across device types (desktop, tablet, mobile) and browsers
- Store and retrieve screenshots
- Webhook callback when ready
- Full-page and viewport captures

## Non-Functional Requirements
- Async processing (2-10s per capture)
- Scale to 10,000 captures/hour
- Durable storage; fast CDN delivery

---

## API Design

```
POST /screenshots
  Body: { "url": "https://example.com", "devices": ["desktop","mobile"],
          "browsers": ["chrome","firefox"], "full_page": true,
          "webhook_url": "https://client.com/callback" }
  Response: { "job_id": "job-abc123", "status": "queued" }

GET /screenshots/{job_id}
  Response: { "status": "completed", "results": [
    { "device": "desktop", "browser": "chrome", "url": "https://cdn.../..." }
  ]}
```

---

## Architecture

```
Client → API Gateway → Screenshot Service
    ↓
PostgreSQL (job records)
    ↓
Kafka (screenshot-tasks topic, one per device×browser)
    ↓
Screenshot Workers (Puppeteer/Playwright)
    ├── Render page at specified viewport
    ├── Capture screenshot
    └── Upload to S3 → Update job record
    ↓
Job Tracker → Webhook callback when all done
    ↓
CloudFront CDN serves screenshots
```

---

## Data Model

```sql
CREATE TABLE screenshot_jobs (
  job_id UUID PRIMARY KEY, url TEXT,
  status VARCHAR(20), webhook_url TEXT,
  created_at TIMESTAMP, completed_at TIMESTAMP
);
CREATE TABLE screenshot_results (
  result_id UUID PRIMARY KEY, job_id UUID,
  device VARCHAR(20), browser VARCHAR(20),
  cdn_url TEXT, status VARCHAR(20), captured_at TIMESTAMP
);
```

---

## Viewport Sizes

| Device | Width | Height |
|--------|-------|--------|
| Desktop | 1920 | 1080 |
| Tablet | 768 | 1024 |
| Mobile | 390 | 844 |

---

## Worker Steps
1. Pull task from Kafka
2. Launch headless browser with specified viewport
3. Navigate to URL (timeout: 30s)
4. Wait for `networkidle` / DOM stable
5. Capture PNG
6. Upload to S3: `screenshots/{job_id}/{device}-{browser}.png`
7. Update result record

---

## Webhook Retry
- POST to webhook_url with results
- Retry with exponential backoff (3 attempts)

---

## Summary
Screenshot service = async job processing. Kafka queues tasks; headless browser workers process independently; S3 + CDN handles delivery. Webhooks decouple client from polling.

---

# Design a Network Connection Path Analyzer

**Difficulty:** Easy

## Problem Statement
Design a system that analyzes network paths between nodes (like traceroute), showing hops, latency, packet loss, and bottlenecks.

---

## Functional Requirements
- Run path analysis between source and destination
- Show each hop: IP, hostname, latency, packet loss
- Detect bottlenecks and high-latency hops
- Store history for trend analysis
- Scheduled recurring checks

---

## API Design

```
POST /analysis
  Body: { "source": "10.0.0.1", "destination": "8.8.8.8", "protocol": "ICMP" }
  Response: { "trace_id": "t-123", "status": "running" }

GET  /analysis/{trace_id}
  Response: { "hops": [
    { "hop": 1, "ip": "...", "latency_ms": 1.2, "packet_loss_pct": 0 }
  ]}

POST /monitors
  Body: { "source": "...", "destination": "...", "interval_minutes": 5 }
```

---

## Architecture

```
API Gateway → Trace Service → Task Queue
    ↓
Probe Agent Pool (distributed, multiple regions)
    ↓ (raw socket ICMP/UDP traceroute)
Result Collector → TimescaleDB (hop data)
                 → PostgreSQL (monitors)
    ↓
Analytics Service → Bottleneck detection, alerting
    ↓
Dashboard (D3.js graph visualization)
```

---

## Probe Algorithm (ICMP TTL)

```
1. Send ICMP echo with TTL=1 → first router replies TTL exceeded
2. Record IP + latency (avg of 3 probes)
3. Increment TTL, repeat until destination or max hops (30)

Per hop: latency = RTT average of 3 probes
Packet loss = (sent - received) / sent × 100
```

---

## Data Model

```sql
CREATE TABLE traces (
  trace_id UUID PRIMARY KEY, source_ip INET,
  destination_ip INET, protocol VARCHAR(10),
  status VARCHAR(20), started_at TIMESTAMP
);

CREATE TABLE hops (
  hop_id UUID PRIMARY KEY, trace_id UUID,
  hop_number INT, ip_address INET,
  hostname VARCHAR(200), latency_ms FLOAT,
  packet_loss FLOAT, as_number INT
);
```

---

## Bottleneck Detection

```python
def detect_bottlenecks(hops):
    bottlenecks = []
    for i in range(1, len(hops)):
        if hops[i].latency_ms - hops[i-1].latency_ms > 20:  # 20ms spike
            bottlenecks.append(hops[i])
        if hops[i].packet_loss > 5:
            bottlenecks.append(hops[i])
    return bottlenecks
```

---

## Monitoring Alerts
- If latency exceeds baseline by 2σ → alert via PagerDuty/Slack

## Graph Visualization
- Directed graph: nodes = hops, colored by latency tier
- Green <10ms, Yellow <50ms, Red >50ms
- Built with D3.js or Cytoscape.js

---

## Summary
Network path analyzer = traceroute logic in an async job system. Distributed probe agents give accurate visibility. TimescaleDB stores hop-level time-series data for trend analysis.

---

# Design an Employee Swap System

**Difficulty:** Easy

## Problem Statement
Design a shift-swap system where employees request to swap shifts, with eligibility validation and manager approval.

---

## Functional Requirements
- Employees post shifts for swap
- Request to take another's shift
- Validate eligibility (role, hours, overtime)
- Manager approval workflow
- Notifications for requests and approvals
- Audit trail

---

## API Design

```
POST /shifts/{shift_id}/swap-requests
  Body: { "requesting_employee_id": "emp-456" }

GET  /shifts/available-swaps?role=cashier&date=2024-01-15
PUT  /swap-requests/{id}/approve    (manager)
PUT  /swap-requests/{id}/reject     (manager)
PUT  /swap-requests/{id}/withdraw   (employee)
GET  /swaps/audit?department=sales
```

---

## Data Model

```sql
CREATE TABLE shifts (
  shift_id UUID PRIMARY KEY, employee_id UUID,
  department VARCHAR(50), role VARCHAR(50),
  shift_date DATE, start_time TIME, end_time TIME,
  swap_requested BOOLEAN DEFAULT FALSE
);

CREATE TABLE swap_requests (
  swap_request_id UUID PRIMARY KEY, shift_id UUID,
  original_employee UUID, requesting_employee UUID,
  status VARCHAR(30),   -- pending, approved, rejected, withdrawn
  manager_id UUID, created_at TIMESTAMP, notes TEXT
);

CREATE TABLE swap_audit_log (
  log_id UUID PRIMARY KEY, swap_id UUID,
  action VARCHAR(50), actor_id UUID,
  actor_role VARCHAR(20), timestamp TIMESTAMP, details JSONB
);
```

---

## Eligibility Validation

```java
class EligibilityValidator {
    ValidationResult validate(Shift shift, Employee requestor) {
        if (!requestor.role.equals(shift.requiredRole)) return FAIL("Role mismatch");
        if (hasConflictingShift(requestor, shift.date))  return FAIL("Scheduling conflict");
        if (wouldExceedOvertimeLimit(requestor, shift))  return FAIL("Overtime limit");
        return PASS;
    }
}
```

---

## Approval Workflow

```
Employee A posts shift
    ↓
Employee B submits swap request
    ↓
Eligibility Check → FAIL → Reject with reason
    ↓ PASS
Notify Manager
    ↓
Manager Review → Approve or Reject
    ↓
Update shifts + Notify both employees
```

---

## Preventing Double-Swap (Concurrency)

```sql
UPDATE swap_requests SET status = 'approved'
WHERE swap_request_id = ? AND status = 'pending'
-- If 0 rows updated: another manager already approved
```

---

## Notifications

| Event | Recipient | Channel |
|-------|-----------|---------|
| Swap requested | Shift owner + Manager | Push + Email |
| Swap approved | Both employees | Push + Email |
| Swap rejected | Requestor | Push |

---

## Summary
Shift swap = workflow + state machine problem. Clean state transitions, atomic DB updates for concurrency, and comprehensive notifications are the core concerns.

---

# Design a Conference Room Booking System

**Difficulty:** Easy

## Problem Statement
Design a room booking system for employees to search available rooms, make reservations, and manage bookings with calendar integration.

---

## Functional Requirements
- Browse rooms by time, capacity, location, equipment
- Book a room for a time range
- Cancel/modify bookings
- Recurring bookings (daily, weekly)
- Google Calendar / Outlook integration
- Email confirmations and reminders

## Non-Functional Requirements
- Prevent double-booking (strong consistency)
- Sub-second availability queries
- Correct timezone handling

---

## API Design

```
GET  /rooms?capacity=10&equipment=projector&building=HQ
GET  /rooms/{room_id}/availability?date=2024-01-15
POST /bookings
  Body: { "room_id": "r-101", "title": "Sprint Planning",
          "start_datetime": "2024-01-15T10:00:00",
          "end_datetime":   "2024-01-15T11:00:00",
          "attendees": ["user@co.com"] }
PUT  /bookings/{booking_id}
DELETE /bookings/{booking_id}
```

---

## Data Model

```sql
CREATE TABLE rooms (
  room_id  UUID PRIMARY KEY, name VARCHAR(100),
  building VARCHAR(50), floor INT, capacity INT,
  equipment TEXT[]
);

CREATE TABLE bookings (
  booking_id     UUID PRIMARY KEY,
  room_id        UUID REFERENCES rooms,
  organizer_id   UUID,
  title          VARCHAR(200),
  start_datetime TIMESTAMPTZ NOT NULL,
  end_datetime   TIMESTAMPTZ NOT NULL,
  status         VARCHAR(20) DEFAULT 'confirmed',
  recurrence_id  UUID,
  CONSTRAINT no_overlap EXCLUDE USING gist (
    room_id WITH =,
    tstzrange(start_datetime, end_datetime) WITH &&
  )
);
```

The `EXCLUDE USING gist` constraint prevents overlapping bookings at the DB level.

---

## Architecture

```
Web/Mobile Client → API Gateway
    ↓
Booking Service → PostgreSQL (rooms, bookings)
    ↓            Redis Cache (availability)
Calendar Integration Service → Google Calendar API / Outlook API
    ↓
Notification Service → Email reminders (15 min before)
```

---

## Booking Conflict Prevention

1. DB-level: PostgreSQL range exclusion constraint
2. App-level: Check availability before insert
3. Transaction: INSERT inside transaction; constraint violation → "Room unavailable"

---

## Availability Query (Redis Cache)
- Compute free slots per room per day
- Cache in Redis (TTL: 5 min, invalidate on new booking)
- Avoids DB scan on every availability request

---

## Recurring Bookings
- Pre-generate all instances (up to 1 year) with same `recurrence_id`
- Cancel all: `DELETE WHERE recurrence_id = ?`
- Cancel one: `DELETE WHERE booking_id = ?`

---

## Calendar Integration
- On booking creation: create Google/Outlook event via OAuth
- Add attendees → calendar invites auto-sent
- Store `external_cal_id` for update/delete sync

---

## Summary
Room booking = scheduling conflict problem. PostgreSQL range exclusion is the correctness cornerstone. Redis caches availability. Calendar API integration completes the experience with invites and reminders.

---

# Design a Video View Count System

**Difficulty:** Easy

## Problem Statement
Design a scalable video view count system (like YouTube's) that accurately tracks and displays view counts across billions of daily views.

---

## Functional Requirements
- Increment view count when a video is watched
- Return current view count
- Prevent manipulation (bots, refresh spam)
- Near-real-time display (within minutes)
- Historical analytics (views per day/week)

## Non-Functional Requirements
- Handle 1B+ view events/day (100K+ peak QPS)
- Exact counts; some display lag acceptable
- Idempotent (dedup duplicate events)
- Low read latency (<50ms)

---

## Capacity Estimation

| Metric | Value |
|--------|-------|
| Views/day | 1B |
| Write QPS | 12K avg, 100K peak |
| Read QPS | ~1M |
| Raw events (30d) | ~6 TB |

---

## Architecture

```
Client (after 30s of watch time)
    ↓ POST /views
View Event Service (deduplication + publish)
    ↓
Kafka (view-events topic)
    ↓
Stream Aggregator (Flink)   Raw Event Store (S3/ClickHouse)
    ↓
Redis (live counts via INCR)   PostgreSQL (daily aggregates)
    ↓
Read API → GET /videos/{id}/view-count
```

---

## Deduplication Strategy

**Session-level** (same session):
```redis
BF.ADD views:video-abc123:session session-id
-- Bloom filter, TTL 24h
```

**User-level** (same user, multiple sessions):
```redis
SADD views:video-abc123:users:20240101 user-123
-- Only count if new; TTL 48h
```

---

## Write Flow
1. Client sends view after 30s watch time
2. View Event Service checks Bloom filter
3. If not duplicate → publish to Kafka
4. Flink aggregates every 60s → `INCR views:video-abc123` in Redis
5. Hourly → persist to PostgreSQL `video_daily_views`

---

## Database Schema

```sql
CREATE TABLE video_daily_views (
  video_id  VARCHAR(20),
  view_date DATE,
  view_count BIGINT,
  PRIMARY KEY (video_id, view_date)
);
```

---

## Bot/Spam Prevention

| Technique | Description |
|-----------|-------------|
| Watch duration | Minimum 30s to count |
| Rate limiting | Max N views/IP/hour |
| Bloom filter | Session dedup |
| ML classifier | Detect automated patterns |
| Delayed counting | Verified after ~24h |

---

## Trade-offs

| Concern | Decision |
|---------|----------|
| Consistency | Eventual (60s lag) — acceptable |
| Deduplication | Bloom filter (fast, small false positive rate) |
| Aggregation | Flink stream processing |
| Raw event storage | S3 for reprocessing ability |

---

## Summary
Kafka decouples ingestion, Flink aggregates in streaming fashion, Redis provides fast real-time counts, S3 retains raw events for audit. The 30s threshold and Bloom filter handle the most common spam vectors.

---

## 🟡 Medium (55 Problems)

# Design Twitter

**Difficulty:** Medium

## Problem Statement
Design Twitter — a social platform where users post tweets, follow others, and see a personalized home timeline.

---

## Functional Requirements
- Post tweets (280 chars, optional media)
- Follow/unfollow users
- Home timeline (followed users' tweets)
- Like, retweet, reply
- Search, trending topics
- Notifications

## Non-Functional Requirements
- 500M DAU, 350K tweets/min peak
- Home timeline <200ms
- 99.99% availability
- Read-heavy (10:1 ratio)

---

## Capacity Estimation

| Metric | Value |
|--------|-------|
| Tweets/day | 500M |
| Read QPS (timeline) | ~5M/sec peak |
| Media storage | ~20 TB/day |

---

## Core Design: Fan-Out on Write

When a user tweets:
1. Store tweet in Cassandra
2. Publish to Kafka
3. Fan-out workers: `LPUSH timeline:{follower_id} tweet_id` for each follower
4. `LTRIM timeline:{follower_id} 0 799`

Timeline read: `LRANGE timeline:{user_id} 0 19` → fetch tweet content from Cassandra

### Celebrity Problem (Fan-Out on Read)
- Users >1M followers: don't fan-out on write
- On timeline read: merge pre-computed timeline + live celeb tweets
- Threshold: >1M followers → hybrid model

---

## Data Models

### Tweets (Cassandra)
```
tweet_id   TimeUUID  [partition key — enables time-ordering]
user_id    UUID
text       TEXT
created_at TIMESTAMP
likes      COUNTER
```

### Follows (MySQL)
```sql
CREATE TABLE follows (
  follower_id BIGINT, followee_id BIGINT,
  PRIMARY KEY (follower_id, followee_id)
);
```

### Timeline (Redis)
```
Key: timeline:{user_id} | Type: LIST of tweet_ids | Max: 800 entries
```

---

## Architecture

```
Clients → Load Balancer + CDN
    ↓
Tweet Service / Timeline Service / User Service
    ↓
Cassandra (tweets)    Redis Cluster (timelines)
MySQL (users/follows) Elasticsearch (search)
Kafka (fan-out)       S3 (media), CDN
```

---

## Trending Topics

```redis
ZINCRBY trending:hashtags:{hour_bucket} 1 "#java"
ZREVRANGE trending:hashtags:{hour_bucket} 0 9   → top 10
```

---

## Trade-offs

| Concern | Decision |
|---------|----------|
| Timeline | Fan-out write (low read latency) + hybrid for celebs |
| Tweet storage | Cassandra (write-heavy, time-series) |
| Search | Elasticsearch (async indexed) |

---

## Summary
Twitter's core challenge is timeline generation. Fan-out on write delivers sub-100ms reads. Celebrity hybrid model prevents write amplification. Cassandra's time-ordered storage fits tweet patterns perfectly.

---

# Design Facebook Messenger

**Difficulty:** Medium

## Problem Statement
Design Facebook Messenger — real-time messaging with 1-1 chats, group chats, delivery receipts, and media sharing.

---

## Functional Requirements
- 1-on-1 and group messaging (up to 256 members)
- Message delivery receipts: Sent / Delivered / Read
- Media sharing (images, videos, files)
- Message history
- Online presence / last seen
- Push notifications for offline users

## Non-Functional Requirements
- Real-time delivery (<500ms for online users)
- Messages never lost
- 1B+ users; 100B+ messages/day

---

## Capacity Estimation

| Metric | Value |
|--------|-------|
| Messages/day | 100B |
| Active WebSocket connections | 500M |
| Media uploads | ~500 TB/day |

---

## Architecture

```
Client (WebSocket)
    ↓
WebSocket Gateway (sticky sessions, consistent hashing)
    ↓
Chat Service → Kafka (message events)
                    ↓
            Fanout Service → push to online users via WebSocket
                    ↓ (if offline)
            FCM/APNs (push notifications)
    ↓
HBase/Cassandra (messages) | Redis (presence)
S3 + CDN (media)
```

---

## Message Storage (Cassandra)

```
PRIMARY KEY ((conversation_id), message_id DESC)

conversation_id  UUID
message_id       TIMEUUID      -- enables time-ordering
sender_id        UUID
content          TEXT
status           VARCHAR       -- sent, delivered, read
created_at       TIMESTAMP
```

Partition by `conversation_id` → all messages for a conversation co-located.

---

## Message Delivery Flow

```
Sender → Chat Service
    ├── Store in Cassandra (status: sent)
    └── Publish to Kafka
            ↓
    Fanout Consumer per recipient:
        ├── Online → push via WebSocket → ACK → status: delivered
        └── Offline → push notification → delivered on next open
```

---

## Message Ordering
- TIMEUUID (Cassandra) ensures ordering within conversation
- Client sequence numbers detect gaps (resync if needed)

---

## Group Chat
- Message stored once; fanout to each member
- For >100 member groups: async Kafka workers
- `conversation_members(conversation_id, user_id, last_read_message_id)`

---

## Delivery Receipts

| Status | Trigger |
|--------|---------|
| Sent | Message stored in DB |
| Delivered | WebSocket ACK from recipient device |
| Read | Recipient opened conversation |

---

## Trade-offs

| Concern | Decision |
|---------|----------|
| Storage | Cassandra (write-heavy, conversation-partitioned) |
| Real-time | WebSocket + Redis user→gateway routing |
| Large groups | Async Kafka fanout |

---

## Summary
Messenger's core challenge is real-time delivery at scale. WebSocket gateways with Redis routing enable direct user-to-connection addressing. Cassandra conversation-partitioned schema enables efficient history retrieval.

---

# Design an API Rate Limiter

**Difficulty:** Medium

## Problem Statement
Design a distributed API rate limiter that restricts requests per client within time windows, protecting backend services from abuse.

---

## Functional Requirements
- Per-user, per-IP, per-API-key limits
- Multiple windows (1min, 1hr, 1day)
- HTTP 429 with retry info when exceeded
- Different limits per endpoint/tier (free vs premium)
- Rate limit headers in every response

## Non-Functional Requirements
- <5ms overhead per check
- Highly available (don't block API if limiter is down)
- Works across multiple API server instances

---

## Algorithms Comparison

| Algorithm | Pros | Cons |
|-----------|------|------|
| Fixed Window Counter | Simple | Burst at boundary |
| Sliding Window Log | Accurate | High memory |
| **Sliding Window Counter** | Accurate + efficient | Approximation |
| Token Bucket | Allows burst | Complex |
| Leaky Bucket | Smooth output | Queuing complexity |

**Recommended: Sliding Window Counter**

```
current_count = prev_window_count × (1 - elapsed%) + curr_window_count

E.g.: prev=80, current=30, elapsed=70%
approx = 80×0.30 + 30 = 54 → under limit of 100
```

---

## Response Headers

```
X-RateLimit-Limit:     100
X-RateLimit-Remaining: 63
X-RateLimit-Reset:     1704067260
Retry-After:           45          (only on 429)
```

---

## Architecture

```
Client Request → API Gateway (Rate Limiter Middleware)
    ↓
Rate Limit Service
    ├── Redis Cluster (counters, atomic operations)
    └── Config Store (rules: MySQL/etcd, cached in memory)
    ↓
Allow → Backend API   |   Deny → 429 Too Many Requests
```

---

## Redis Implementation

### Fixed Window
```
key: rate:{user_id}:{endpoint}:{window_start}
INCR key → count; EXPIRE key window_seconds
```

### Sliding Window (Sorted Set)
```
ZADD key now_ms request_id
ZREMRANGEBYSCORE key 0 (now - window_ms)
count = ZCOUNT key (now - window_ms) +inf
EXPIRE key window_ms
```

### Atomic Check (Lua Script)
```lua
local tokens = redis.call('GET', key)
if tokens > 0 then redis.call('DECR', key); return 1
else return 0 end
```

---

## Rate Limit Rules

```json
[
  { "key": "user_id", "endpoint": "/search", "limit": 10, "window_sec": 60 },
  { "key": "api_key", "tier": "free",  "limit": 1000, "window_sec": 3600 },
  { "key": "api_key", "tier": "pro",   "limit": 10000, "window_sec": 3600 }
]
```

---

## Distributed Approach

- Redis Cluster: hash slots across nodes
- Local in-process counter per pod (sync every 100ms): reduces Redis latency
- Accept slight over-limit during sync window

---

## Failover Strategy

If Redis unavailable:
- **Fail open**: allow all → protect availability
- **Fail closed**: deny all → protect backend
- **Circuit breaker**: auto-switch to fail-open

---

## Summary
Distributed rate limiter = atomic check-and-increment in shared store. Redis + Lua scripts ensure atomicity. Sliding window counter is the best accuracy-to-performance tradeoff. Fail-open on Redis downtime protects API availability.

---

# Design Youtube or Netflix

**Difficulty:** Medium

## Problem Statement
Design a video streaming platform handling upload, transcoding, storage, and adaptive bitrate streaming to millions of concurrent viewers.

---

## Functional Requirements
- Upload videos
- Transcode to multiple resolutions (360p–4K)
- Adaptive bitrate streaming
- Search, recommendations
- Comments, likes, subscriptions

## Non-Functional Requirements
- 2B users; 500 hrs uploaded/min
- 1B hours watched/day
- Startup latency <2s
- Global delivery

---

## Capacity Estimation

| Metric | Value |
|--------|-------|
| Upload rate | 500 hrs/min |
| Daily raw storage | ~60 TB |
| Transcoded (5× multiplier) | 300 TB/day |
| CDN bandwidth | ~10 Tbps peak |

---

## Upload & Processing Pipeline

```
Client → presigned S3 URL (bypass app servers)
S3 upload → S3 event → SQS/Kafka
Transcoding Workers (FFmpeg):
    ├── Resolutions: 360p, 480p, 720p, 1080p, 4K
    ├── Formats: H.264, H.265, VP9, AV1
    └── Output: HLS segments (2-10s chunks) + manifest

Segments stored in S3:
  /videos/abc123/720p/segment_000.ts
  /videos/abc123/manifest.m3u8

CDN distributes segments globally
```

---

## Adaptive Bitrate Streaming (HLS)

```
#EXTM3U
#EXT-X-STREAM-INF:BANDWIDTH=500000,RESOLUTION=640x360
360p/playlist.m3u8
#EXT-X-STREAM-INF:BANDWIDTH=2000000,RESOLUTION=1280x720
720p/playlist.m3u8
```

Player logic:
- Buffer <5s → drop quality
- Buffer >20s → try higher quality
- Bandwidth estimate guides initial selection

---

## Architecture

```
Upload Client → Upload Service → S3 (raw) → Kafka → Transcoding Workers
                                                          ↓
                                                    S3 (segments)
                                                          ↓
Play Client → API Gateway → Metadata Service (MySQL)    CDN Edge
                                        ↓
                                    CDN (HLS segments)
```

---

## CDN Strategy
- Segments are immutable → TTL 365 days
- Edge pre-warm popular content
- Origin: S3 multi-region (US, EU, APAC)

---

## Video Metadata (MySQL + Redis)

```sql
CREATE TABLE videos (
  video_id VARCHAR(20) PRIMARY KEY, title VARCHAR(300),
  channel_id BIGINT, duration_sec INT,
  resolutions JSON, status VARCHAR(20),
  upload_date TIMESTAMP, views BIGINT
);
```

---

## Search & Recommendations
- Elasticsearch: title, description, tags
- Recommendations: collaborative filtering ML model
- Trending: view velocity in last 1h (Redis sorted set)

---

## Content Moderation
- ML scan on upload: nudity, violence, copyright
- Flagged → manual review queue
- Content ID fingerprinting for copyright

---

## Summary
Video streaming = data pipeline + CDN. The upload→transcode→segment→CDN pipeline is core. ABR ensures smooth playback. CDN edge caching makes 1B hours/day possible without crushing origin servers.

---

# Design Typeahead Suggestion

**Difficulty:** Medium

## Problem Statement
Design a typeahead/autocomplete system (like Google Search) returning relevant suggestions in real-time as users type.

---

## Functional Requirements
- Top-N suggestions for a prefix in <100ms
- Suggestions ranked by popularity
- Personalized suggestions (optional)
- Trend-aware
- Multiple languages

## Non-Functional Requirements
- P99 latency <100ms globally
- 100M users; 10K suggestions/sec peak
- Near-real-time trend updates

---

## API Design

```
GET /suggest?prefix=java&limit=10
  Response: { "suggestions": [
    { "query": "java spring boot", "score": 9823451 },
    { "query": "java interview questions", "score": 7234211 }
  ]}
```

---

## Core Data Structure: Trie

Each trie node stores: top-10 suggestions by score for that prefix.

```
root → j → a → v → a → [java, java spring, java interview...]
```

---

## Architecture

```
Clients → CDN (edge cache for popular prefixes)
    ↓
Suggestion Service (stateless)
    ├── Redis Cluster (hot prefix cache, TTL 10 min)
    └── Trie Servers (in-memory, sharded by first char)

Offline pipeline:
Search Logs → Kafka → Aggregator (Spark, hourly)
    → Trie Builder → Trie Servers (hot swap, blue-green)
```

---

## Trie Sharding
- Shard by first character: 'a' → server-1, 'b' → server-2
- Each shard: ~2-5 GB in-memory sub-trie

---

## Ranking Score

```
score = sum(query_count_per_day × 0.9^days_ago)  [last 30 days]
```

Top-10 by score pre-computed at each trie node.

---

## Trie Building Pipeline
1. Search logs → Kafka
2. Spark batch (hourly): count frequencies + apply decay
3. Compute top-k per prefix
4. Build new trie, hot-swap (blue-green deploy)
5. Real-time trending overlay via Redis (within-hour spikes)

---

## CDN Edge Caching
- Short prefixes ("a", "ja") → millions share same suggestions
- CDN edge TTL: 5 minutes
- Offloads 80%+ of suggestion traffic at edge

---

## Trade-offs

| Concern | Decision |
|---------|----------|
| Data structure | Trie (O(prefix_length) lookup) |
| Updates | Hourly batch + Redis trending overlay |
| Sharding | By first character |
| Cache | Redis + CDN (two-tier) |

---

## Summary
Typeahead = prefix lookup. Trie with pre-computed top-k at each node gives fast lookup. Two-tier cache (Redis + CDN) absorbs massive read volume. Hourly batch + Redis overlay balances freshness and performance.

---

# Design Twitter Search

**Difficulty:** Medium

## Problem Statement
Design Twitter Search — full-text tweet search with near-real-time indexing across billions of tweets.

---

## Functional Requirements
- Full-text search on tweet content
- Search by hashtag, mention, user
- Filter: date range, media, min likes
- Sort: latest, top (relevance)
- New tweets searchable within 30 seconds
- Trending search suggestions

---

## Architecture

```
Tweet Created → Kafka (tweet-events)
    ↓
Indexing Service (consumer)
    ↓
Elasticsearch Cluster (inverted index)
    ↓
Search API → Redis (hot query cache)
    ↓
Client
```

---

## Elasticsearch Index

```json
{
  "mappings": { "properties": {
    "tweet_id":   { "type": "keyword" },
    "text":       { "type": "text", "analyzer": "english" },
    "hashtags":   { "type": "keyword" },
    "mentions":   { "type": "keyword" },
    "created_at": { "type": "date" },
    "likes":      { "type": "integer" }
  }}
}
```

---

## Query Processing

```json
{
  "bool": {
    "must": [{ "match": { "text": "java spring boot" }}],
    "filter": [
      { "range": { "created_at": { "gte": "2024-01-01" }}},
      { "range": { "likes": { "gte": 100 }}}
    ]
  }
}
```

Sort options: `_score` (relevance) or `created_at` (latest).

---

## Real-Time Indexing (30s target)
1. New tweet → Kafka
2. Consumer reads tweet, extracts text/hashtags/mentions
3. Elasticsearch `index` API — visible in ~1s (default refresh interval)

---

## Relevance Scoring (Top sort)

```
score = BM25_text_score
      + log(likes+1) × 0.3
      + log(retweets+1) × 0.5
      + recency_decay (exponential, 7-day halflife)
      + verified_boost × 0.1
```

Using Elasticsearch `function_score` query.

---

## Scaling
- Time-based indices (weekly): efficient range queries, easy archival
- 2 replicas per shard for read scale + HA
- ILM: hot → warm → cold tier by tweet age

---

## Trending Topics
- Elasticsearch aggregations on `hashtags` for last 1 hour
- Or Redis sorted set: `ZINCRBY trending:hashtags java 1` per minute bucket

---

## Summary
Twitter Search = Elasticsearch problem. Kafka consumers keep index fresh within 30s. Custom function_score balances text match with engagement. Time-based indices enable efficient range filtering and data lifecycle.

---

# Design Instagram

**Difficulty:** Medium

## Problem Statement
Design Instagram — a photo/video sharing social network with feeds, stories, explore, and DMs.

---

## Functional Requirements
- Upload photos/videos with captions and hashtags
- Follow/unfollow users
- Home feed (followed users' posts)
- Stories (24-hour expiry)
- Explore/Discover page
- Likes, comments, saves
- Direct messages
- Reels (short videos)

## Non-Functional Requirements
- 2B MAU, 500M DAU
- 100M photos + 20M videos uploaded/day
- Feed load <500ms

---

## Capacity Estimation

| Metric | Value |
|--------|-------|
| Photo uploads/day | 100M × 3MB = 300 TB |
| Video uploads/day | 20M × 50MB = 1 PB |
| Feed reads/day | 5B |

---

## Architecture

```
Mobile/Web → CDN (images, videos)
    ↓
API Gateway
    ↓
Post Service   → Cassandra (posts)
Feed Service   → Redis (feeds) + PostgreSQL (backfill)
User Service   → PostgreSQL (users, follows)
    ↓
S3 + CDN (media) | Kafka (fan-out, notifications)
```

---

## Photo Upload Flow
1. Client requests pre-signed S3 URL
2. Client uploads directly to S3
3. POST service stores metadata in Cassandra
4. Kafka event → image resizing, feed fan-out, notifications

---

## Feed Generation

### Fan-out on Write (Push)
```redis
LPUSH feed:{follower_id} post_id
LTRIM feed:{follower_id} 0 999
```

### Celebrity Hybrid (>500K followers)
- Don't fan-out on write
- On feed read: merge cached feed + live celeb posts

### Feed Ranking
- ML model scores: interest × recency × relationship strength
- Not pure reverse-chronological

---

## Data Models

### Posts (Cassandra)
```
post_id UUID, user_id UUID, media_urls LIST<TEXT>,
caption TEXT, hashtags LIST<TEXT>,
likes_count COUNTER, created_at TIMESTAMP
```

### Follow Graph (PostgreSQL + Redis)
```sql
CREATE TABLE follows (
  follower_id BIGINT, followee_id BIGINT, PRIMARY KEY (follower_id, followee_id)
);
```

---

## Stories
- 24-hour TTL in Cassandra
- Story ring: check if any unseen stories → show ring
- Story view tracking: set of viewer user_ids

---

## Media Storage

| Layer | Technology |
|-------|-----------|
| Upload | S3 multi-part |
| Processing | Lambda (resize, compress) |
| Storage | S3 |
| Delivery | CloudFront CDN |

---

## Notifications
- Kafka: interactions (likes, comments, follows)
- Batch + dedup: "3 people liked your photo"
- FCM (Android), APNs (iOS)

---

## Summary
Instagram = media-heavy, feed-driven social network. S3 + CDN for media is mandatory. Hybrid fan-out solves write amplification. ML-ranked feeds drive engagement over simple chronological order.

---

# Design an Online Chess Service

**Difficulty:** Medium

## Problem Statement
Design an online chess service for real-time gameplay, matchmaking, leaderboards, and spectator mode.

---

## Functional Requirements
- ELO-based matchmaking
- Real-time gameplay with server-side move validation
- Blitz, rapid, classical game types
- Spectator mode
- Game history and replay
- ELO rating system
- Leaderboards

## Non-Functional Requirements
- Move round-trip <100ms
- 100,000 concurrent games
- Server-side validation (anti-cheat)

---

## Architecture

```
Client (WebSocket)
    ↓
WebSocket Game Server (per game room)
    ├── Move Validator (chess engine library)
    └── Game State → Redis (active games)
    ↓
PostgreSQL (completed games, ratings)
Matchmaking Service → Redis (ELO sorted queue)
Leaderboard        → Redis sorted set
```

---

## Game Room Design

```
Game Room {game_id}:
  - White player WebSocket connection
  - Black player WebSocket connection
  - N spectator WebSocket connections
  - Game state: FEN string (chess position)
  - Clock: Redis TTL per player
```

Both players route to same game server (consistent hashing by game_id).

---

## Move Validation (Server-Side)

```java
class MoveValidator {
    Chess engine = new Chess(currentFEN);

    MoveResult applyMove(Move move) {
        if (!engine.isLegalMove(move)) return ILLEGAL;
        engine.move(move);
        return new MoveResult(engine.getFEN(),
                              engine.isCheckmate(),
                              engine.isStalemate());
    }
}
```

Uses established chess libraries (Stockfish, chess.js). Never trust client.

---

## Game State

**Redis** (active games):
```
game:{id}:fen     → current FEN string
game:{id}:moves   → LIST of moves
game:{id}:clock_w → white seconds remaining
game:{id}:clock_b → black seconds remaining
game:{id}:status  → active | completed
TTL: 30 min after last move
```

**PostgreSQL** (completed games):
```sql
CREATE TABLE games (
  game_id UUID PRIMARY KEY, white_id BIGINT, black_id BIGINT,
  game_type VARCHAR(20), result VARCHAR(20),
  pgn TEXT, played_at TIMESTAMP,
  white_elo_change INT, black_elo_change INT
);
```

---

## Matchmaking
1. `ZADD queue:{game_type} {elo} {player_id}`
2. Matchmaker runs every second: find players within ELO ±100
3. Match found → remove from queue, create game room
4. Waiting >10s → widen ELO range by 50 every 5s

---

## ELO Update

```
K = 32
expected_A = 1 / (1 + 10^((ratingB - ratingA)/400))
new_rating_A = ratingA + K × (actual_score_A - expected_A)
```

---

## Summary
Online chess demands sub-100ms move round-trips → WebSocket with game server affinity. Server-side move validation via chess engine is non-negotiable. Redis stores live state; PostgreSQL archives completed games.

---

# Design a Web Cache

**Difficulty:** Medium

## Problem Statement
Design a distributed web cache (like Redis/Memcached cluster) that reduces backend load and improves latency.

---

## Functional Requirements
- Store/retrieve key-value pairs
- TTL-based expiry
- Eviction policies (LRU, LFU)
- Cache invalidation
- Distributed across multiple nodes
- Cache-aside, write-through, write-behind patterns

---

## Eviction Policies

| Policy | Best For |
|--------|----------|
| LRU (Least Recently Used) | General purpose |
| LFU (Least Frequently Used) | Stable hot data |
| TTL-based | Time-sensitive data |

---

## LRU Implementation

```java
class LRUCache {
    int capacity;
    Map<String, DLinkedNode> map = new HashMap<>();
    DLinkedNode head, tail;  // doubly linked list

    String get(String key) {
        if (!map.containsKey(key)) return null;
        moveToHead(map.get(key));
        return map.get(key).value;
    }

    void put(String key, String value) {
        if (map.containsKey(key)) {
            map.get(key).value = value;
            moveToHead(map.get(key));
        } else {
            DLinkedNode node = new DLinkedNode(key, value);
            addToHead(node); map.put(key, node);
            if (map.size() > capacity) map.remove(removeTail().key);
        }
    }
}
```

O(1) get and put via HashMap + doubly linked list.

---

## Distributed Architecture

```
Cache Client Library (consistent hashing)
    ├── Cache Node 1 (Redis)
    ├── Cache Node 2
    └── Cache Node N
            ↓ (on miss)
        Backend DB
```

### Consistent Hashing
- Keys mapped to virtual ring → find next clockwise node
- Adding/removing nodes: only fraction of keys remapped
- Virtual nodes (150/physical node) → better balance

---

## Cache Patterns

**Cache-Aside (most common):**
```
1. Check cache → HIT: return | MISS: query DB
2. Store in cache → return
```

**Write-Through:** Write to cache AND DB synchronously (consistent, more latency)

**Write-Behind:** Write to cache immediately, async to DB (fast writes, loss risk on crash)

---

## Cache Invalidation

| Strategy | Description |
|----------|-------------|
| TTL expiry | Simplest; stale possible until TTL |
| Event-driven | DB change → Kafka → invalidate key |
| Write-through | Always consistent |

**CDC Pattern:** `DB write → Debezium → Kafka → DELETE cache key`

---

## Multi-Level Cache (L1/L2)

```
Request → L1 (in-process, ~100MB) → HIT: return
              ↓ MISS
          L2 (Redis cluster, ~10TB) → HIT: populate L1, return
              ↓ MISS
          Backend DB → populate L2 + L1 → return
```

---

## Hot Key Problem
- Single popular key → overloads one node
- Solutions:
  1. Replicate: `hot_key_1`, `hot_key_2`, `hot_key_3`; client picks randomly
  2. L1 in-process cache for hottest keys

---

## Thundering Herd
Many simultaneous misses for same key after expiry:
```
First requester: Redis SETNX lock → fetch from DB → update cache → release
Other requesters: wait for lock or return stale value
```

---

## Summary
Distributed cache = LRU eviction + consistent hashing + replication. Multi-level cache maximizes hit rates. Thundering herd and hot key are the main operational challenges to design against.

---

# Design a Task Scheduler

**Difficulty:** Medium

## Problem Statement
Design a distributed task scheduler that schedules and executes tasks reliably, supports cron expressions, one-time delays, and retries.

---

## Functional Requirements
- Schedule: one-time (at time T) and recurring (cron)
- At-least-once execution
- Retry on failure with backoff
- Task status tracking
- Cancel scheduled tasks
- Task priorities
- Dead letter queue for permanently failed tasks

---

## API Design

```
POST /tasks/schedule
  Body: { "task_name": "send_email", "payload": {...},
          "schedule": "one_time", "run_at": "2024-01-15T10:00:00Z",
          "retry_policy": { "max_retries": 3, "backoff_sec": [10, 60, 300] }}

POST /tasks/schedule
  Body: { "task_name": "report", "schedule": "cron",
          "cron_expression": "0 8 * * MON", "timezone": "America/New_York" }

GET  /tasks/{task_id}
DELETE /tasks/{task_id}
```

---

## Architecture

```
API Server (Submit/Cancel)
    ↓
PostgreSQL (task store)
    ↓
Scheduler Service (leader-elected, polls every 1s)
    ↓ FOR UPDATE SKIP LOCKED
Kafka (task-queue, prioritized)
    ↓
Worker Pool (consumer group)
    ├── Execute task
    ├── Update status in DB
    └── On failure: requeue with backoff, or DLQ after max retries
```

---

## Database Schema

```sql
CREATE TABLE tasks (
  task_id          UUID PRIMARY KEY,
  task_name        VARCHAR(100),
  payload          JSONB,
  status           VARCHAR(20),    -- pending, running, completed, failed
  schedule_type    VARCHAR(20),    -- one_time, cron
  run_at           TIMESTAMPTZ,
  cron_expression  VARCHAR(100),
  timezone         VARCHAR(50),
  retry_count      INT DEFAULT 0,
  max_retries      INT DEFAULT 3,
  next_retry_at    TIMESTAMPTZ,
  priority         INT DEFAULT 5   -- 1 (high) to 10 (low)
);

CREATE INDEX idx_pending_due ON tasks (status, run_at)
  WHERE status = 'pending';
```

---

## Scheduler Service (Leader-Elected)

```python
while True:
    if not acquire_leader_lock():   # Redis SETNX, TTL=30s
        sleep(5); continue

    due_tasks = db.query(
        "SELECT * FROM tasks WHERE status='pending' AND run_at <= NOW() + '30s'"
        "ORDER BY priority ASC, run_at ASC LIMIT 1000 FOR UPDATE SKIP LOCKED"
    )
    for task in due_tasks:
        db.update(task.id, status='running')
        kafka.publish('task-queue', task)
    sleep(1)
```

`FOR UPDATE SKIP LOCKED` prevents multiple scheduler instances picking same task.

---

## Worker Execution

```python
def execute_task(task):
    try:
        task_registry[task.task_name](task.payload)
        db.update(task.id, status='completed')
    except Exception:
        if task.retry_count < task.max_retries:
            backoff = backoff_schedule[task.retry_count]
            db.update(task.id, status='pending',
                      retry_count=task.retry_count+1,
                      run_at=now()+backoff)
        else:
            db.update(task.id, status='failed')
            dlq.publish(task)
```

---

## Cron Scheduling
After each execution:
```python
next_run = croniter(task.cron_expression, now()).get_next()
db.update(task.id, status='pending', run_at=next_run)
```

---

## Idempotency (At-Least-Once Safety)

```
Workers include idempotency_key = task_id + attempt_number
Redis SETNX idempotency:{key} 1 EX 86400
If key already set → skip (already executed)
```

---

## Summary
Task scheduler: PostgreSQL task store + leader-elected polling scheduler + Kafka work queue. `FOR UPDATE SKIP LOCKED` prevents duplicate scheduling. Idempotency ensures at-least-once without double execution.

---

# Design an E-commerce Service

**Difficulty:** Medium

## Problem Statement
Design an e-commerce platform (like Amazon) handling product catalog, shopping cart, orders, payments, and inventory.

---

## Functional Requirements
- Product catalog (search, browse, categories)
- Shopping cart (add, remove, update)
- Order placement and management
- Payment processing
- Inventory management
- Order tracking
- Reviews and ratings

---

## Capacity Estimation

| Metric | Value |
|--------|-------|
| Products | 500M |
| Orders/day | 50M |
| Peak QPS (catalog) | 500K |
| Peak QPS (checkout) | 50K |

---

## Architecture

```
Client → CDN (product images, static)
    ↓
API Gateway
    ↓
Product Service  → Elasticsearch + PostgreSQL
Cart Service     → Redis (session-based)
Order Service    → PostgreSQL (ACID transactions)
Payment Service  → Payment Gateway (Stripe/Braintree)
Inventory Service → PostgreSQL + Redis (stock cache)
Notification     → Kafka → Email/SMS
```

---

## Product Catalog

```sql
CREATE TABLE products (
  product_id   UUID PRIMARY KEY,
  title        VARCHAR(500), description TEXT,
  category_id  INT, brand_id INT,
  price        DECIMAL(10,2), sku VARCHAR(50),
  images       TEXT[], attributes JSONB,
  created_at   TIMESTAMP, is_active BOOLEAN
);
```

Elasticsearch synced via CDC for full-text search + faceted filtering.

---

## Shopping Cart (Redis)

```
Key: cart:{user_id}  |  Type: HASH
  product_id → quantity

HSET cart:u123 product-abc 2
HGETALL cart:u123
TTL: 30 days (guest carts shorter)
```

---

## Order Placement Flow

```
1. Client submits order
2. Validate cart items still available
3. Reserve inventory (Redis DECRBY — atomic)
4. Create order in DB (status: pending)
5. Initiate payment → Payment Gateway
6. Payment success → confirm order (status: confirmed)
   Payment failure → release inventory, cancel order
7. Notify fulfillment service via Kafka
8. Email confirmation to customer
```

---

## Inventory Management

```
Redis: stock:{product_id} → available_count  (real-time)
PostgreSQL: inventory table (source of truth)

On purchase: DECRBY stock:{product_id} qty_purchased
If DECRBY result < 0: INCR back → "out of stock"
Periodic sync Redis → PostgreSQL
```

---

## Payment Processing (Saga Pattern)

Use distributed saga for payment + inventory + order:

```
1. Reserve inventory
2. Process payment
3. Confirm order
   If any step fails → compensating transactions (undo previous steps)
```

---

## Search & Discovery

- Elasticsearch: full-text search, faceted filtering (price, brand, category)
- Product recommendations: collaborative filtering + content-based
- Personalization: user purchase history → boosted results

---

## Order States

```
CREATED → PAYMENT_PENDING → PAYMENT_CONFIRMED → PROCESSING
       → SHIPPED → DELIVERED
       → CANCELLED / REFUNDED
```

---

## Trade-offs

| Concern | Decision |
|---------|----------|
| Cart | Redis (fast, session-based) |
| Orders | PostgreSQL (ACID) |
| Inventory | Redis (real-time) + PostgreSQL (durable) |
| Payment | External gateway + saga pattern |

---

## Summary
E-commerce combines a read-heavy product catalog (Elasticsearch + CDN) with write-critical order/payment flows (PostgreSQL + saga). Redis handles cart and real-time inventory. Kafka decouples order fulfillment from payment processing.

---

# Design an Inventory Management System

**Difficulty:** Medium

## Problem Statement
Design an inventory management system for tracking product stock levels across warehouses, managing restocking, and integrating with order fulfillment.

---

## Functional Requirements
- Track inventory levels per product per warehouse
- Reserve/allocate inventory for orders
- Receive stock from suppliers
- Transfer between warehouses
- Low-stock alerts and automatic reorder triggers
- Reporting: stock value, turnover, shrinkage

---

## API Design

```
GET  /inventory?product_id=&warehouse_id=
POST /inventory/reserve
  Body: { "order_id": "...", "items": [{ "product_id": "...", "qty": 2 }] }
POST /inventory/receive
  Body: { "warehouse_id": "...", "items": [{ "product_id": "...", "qty": 100 }] }
POST /inventory/transfer
  Body: { "from_warehouse": "...", "to_warehouse": "...", "product_id": "...", "qty": 50 }
GET  /inventory/alerts
GET  /inventory/reports/stock-value
```

---

## Data Model

```sql
CREATE TABLE inventory (
  inventory_id   UUID PRIMARY KEY,
  product_id     UUID NOT NULL,
  warehouse_id   UUID NOT NULL,
  quantity_on_hand    INT NOT NULL DEFAULT 0,
  quantity_reserved   INT NOT NULL DEFAULT 0,
  quantity_available  INT GENERATED ALWAYS AS (quantity_on_hand - quantity_reserved),
  reorder_point       INT NOT NULL DEFAULT 10,
  reorder_quantity    INT NOT NULL DEFAULT 100,
  last_updated        TIMESTAMP,
  UNIQUE (product_id, warehouse_id)
);

CREATE TABLE inventory_transactions (
  txn_id       UUID PRIMARY KEY,
  product_id   UUID, warehouse_id UUID,
  txn_type     VARCHAR(30),  -- receive, reserve, release, transfer, adjustment
  quantity     INT,
  reference_id UUID,         -- order_id, transfer_id, etc.
  created_at   TIMESTAMP
);
```

---

## Architecture

```
Order Service → Inventory Service
    ↓
PostgreSQL (inventory, transactions)
    ↓
Redis (available stock cache — real-time)
    ↓
Kafka (inventory events → alerts, reorder triggers)
    ↓
Reporting Service → ClickHouse/DWH
```

---

## Reservation Flow (Atomic)

```sql
BEGIN;
SELECT * FROM inventory
WHERE product_id = ? AND warehouse_id = ?
FOR UPDATE;   -- row-level lock

-- Check available = on_hand - reserved >= requested_qty
UPDATE inventory
SET quantity_reserved = quantity_reserved + requested_qty
WHERE product_id = ? AND available >= requested_qty;

INSERT INTO inventory_transactions (...) VALUES (...);
COMMIT;
```

If reservation fails (insufficient qty) → return error, don't place order.

---

## Multi-Warehouse Fulfillment

When a single warehouse can't fulfill an order:
1. Query all warehouses with sufficient stock
2. Rank by proximity to shipping address or cost
3. Split order across multiple warehouses if needed
4. Create separate shipments

---

## Low-Stock Alerts & Reorder

```python
# Kafka consumer checks after each transaction
def check_reorder(product_id, warehouse_id):
    item = db.get_inventory(product_id, warehouse_id)
    if item.quantity_available <= item.reorder_point:
        publish_alert(product_id, warehouse_id)
        if item.auto_reorder:
            create_purchase_order(product_id, item.reorder_quantity)
```

---

## Inventory Accuracy

- **Cycle counting**: periodic physical counts of subset of SKUs
- **Shrinkage tracking**: discrepancy between system and physical count
- **Adjustment transactions**: `txn_type = 'adjustment'` with reason code

---

## Reporting

- Stock value: `SUM(quantity_on_hand × unit_cost)` by category/warehouse
- Inventory turnover: `COGS / avg_inventory_value` per period
- Slow-moving items: products with zero sales in last N days
- Stored in ClickHouse for fast analytical queries

---

## Summary
Inventory management centers on accurate, atomic stock adjustments. Row-level locking in PostgreSQL ensures no overselling. Redis caches available stock for fast real-time checks. Kafka drives alerts and reorder automation.

---

# Design an Online Payment Service

**Difficulty:** Medium

## Problem Statement
Design an online payment service (like Stripe or PayPal) that processes payments, handles refunds, and ensures transaction accuracy and security.

---

## Functional Requirements
- Accept payments (credit card, bank transfer, digital wallets)
- Process refunds
- Transaction history
- Multi-currency support
- Webhook notifications to merchants
- Fraud detection
- Idempotent payment APIs

## Non-Functional Requirements
- ACID transactions (no money lost/double-charged)
- PCI DSS compliance
- High availability (99.999%)
- Low latency (<3s for payment completion)

---

## API Design

```
POST /payments
  Body: { "amount": 2000, "currency": "USD", "source": "card_token",
          "merchant_id": "m-123", "idempotency_key": "order-456-attempt-1" }
  Response: { "payment_id": "pay-789", "status": "succeeded", "amount": 2000 }

POST /refunds
  Body: { "payment_id": "pay-789", "amount": 1000, "reason": "customer_request" }

GET  /payments/{payment_id}
GET  /payments?merchant_id=&created_after=&status=

POST /webhooks  (merchant endpoint, called by our system)
```

---

## Architecture

```
Merchant App → Payment API Gateway
    ↓
Payment Service
    ├── Idempotency Check (Redis)
    ├── Fraud Detection (ML model)
    ├── Card Network Gateway (Visa/Mastercard)
    └── Database (PostgreSQL)
    ↓
Kafka (payment-events) → Webhook Service → Merchant endpoints
                       → Analytics Service
                       → Reconciliation Service
```

---

## Data Model

```sql
CREATE TABLE payments (
  payment_id       UUID PRIMARY KEY,
  merchant_id      UUID NOT NULL,
  amount           BIGINT NOT NULL,   -- in cents
  currency         CHAR(3),
  status           VARCHAR(30),       -- pending, processing, succeeded, failed
  payment_method   VARCHAR(30),
  idempotency_key  VARCHAR(200) UNIQUE,
  processor_ref    VARCHAR(200),      -- card network reference
  created_at       TIMESTAMP,
  updated_at       TIMESTAMP
);

CREATE TABLE refunds (
  refund_id   UUID PRIMARY KEY,
  payment_id  UUID REFERENCES payments,
  amount      BIGINT, status VARCHAR(30),
  reason      VARCHAR(200), created_at TIMESTAMP
);

CREATE TABLE merchant_accounts (
  merchant_id UUID PRIMARY KEY,
  balance     BIGINT DEFAULT 0,   -- in cents
  currency    CHAR(3)
);
```

---

## Idempotency

Critical: prevent double-charging on network retry:

```python
def process_payment(request):
    # Check if already processed
    cached = redis.get(f"idem:{request.idempotency_key}")
    if cached:
        return json.loads(cached)  # return same response

    # Lock: prevent concurrent duplicate
    with redis.lock(f"lock:{request.idempotency_key}", timeout=30):
        # Check DB (in case Redis was cleared)
        existing = db.get_payment_by_idempotency_key(request.idempotency_key)
        if existing:
            return existing

        result = process_new_payment(request)
        redis.setex(f"idem:{request.idempotency_key}", 86400, json.dumps(result))
        return result
```

---

## Payment Processing Flow

```
1. Validate request + idempotency check
2. Fraud scoring (ML model) → block if score > threshold
3. Tokenize card (never store raw card data)
4. Submit to card network (Visa/MC) for authorization
5. Authorization approved → capture payment
6. Update DB: status = succeeded
7. Credit merchant balance
8. Publish event to Kafka → webhook to merchant
```

---

## Ledger / Double-Entry Accounting

For accuracy, use double-entry ledger:
```sql
CREATE TABLE ledger_entries (
  entry_id    UUID PRIMARY KEY,
  account_id  UUID,
  amount      BIGINT,         -- positive = credit, negative = debit
  reference   UUID,
  created_at  TIMESTAMP
);
-- Every payment creates two entries: debit customer, credit merchant
-- Sum of all entries = 0 (double-entry invariant)
```

---

## Fraud Detection

- ML model features: amount, merchant category, velocity, location, device
- Score 0-100; threshold configurable per merchant
- High-risk: 3D Secure challenge, manual review
- Velocity rules: max $X per hour per card

---

## Webhook Delivery
- POST to merchant URL with payment event
- Retry with exponential backoff (1s, 5s, 30s, 5m, 30m, 2h, 8h)
- Merchant must return 200 to acknowledge
- Dead letter after 7 days of failures

---

## Summary
Payment service requires ACID transactions, idempotency, and PCI compliance. Double-entry ledger ensures financial accuracy. Redis-based idempotency prevents double-charging on retries. Kafka drives reliable webhook delivery to merchants.

---

# Design Spotify

**Difficulty:** Medium

## Problem Statement
Design Spotify — a music streaming service with personalized playlists, recommendations, offline downloads, and social features.

---

## Functional Requirements
- Stream music (songs, podcasts)
- Create and manage playlists
- Browse by artist, album, genre
- Personalized recommendations (Discover Weekly)
- Offline download for premium users
- Real-time "currently playing" updates
- Social: follow artists, see what friends listen to
- Lyrics display

## Non-Functional Requirements
- 500M users, 100M DAU
- Low startup latency for music playback (<500ms)
- Seamless cross-device playback
- Global CDN delivery

---

## Capacity Estimation

| Metric | Value |
|--------|-------|
| Songs in catalog | 100M |
| Avg song size | 3MB (320kbps) |
| Total storage | 300 TB catalog |
| Streams/day | 10B |
| Peak concurrent streams | 50M |

---

## Architecture

```
Mobile/Desktop Client
    ↓
API Gateway + CDN
    ↓
Stream Service    → CDN (audio segments)
Catalog Service   → PostgreSQL + Elasticsearch
Playlist Service  → PostgreSQL + Redis (cache)
Recommendation    → ML Platform (offline batch + online serving)
Offline Download  → Encrypted local storage on device
Social/Activity   → Kafka + Redis (friend activity feed)
```

---

## Audio Streaming

### Format & Quality Tiers
| Quality | Bitrate | Size/min |
|---------|---------|---------|
| Low (mobile data) | 24 kbps | 180 KB |
| Normal | 96 kbps | 720 KB |
| High | 160 kbps | 1.2 MB |
| Very High (Premium) | 320 kbps | 2.4 MB |

### HLS-like Chunking
- Songs split into ~10s chunks
- Client prefetches next 2-3 chunks ahead
- Bitrate adaptive based on bandwidth

### CDN Strategy
- Popular songs pre-loaded on edge nodes
- Long TTL (songs are immutable)
- Regional CDN nodes close to users

---

## Data Models

### Songs (PostgreSQL)
```sql
CREATE TABLE songs (
  song_id    UUID PRIMARY KEY, title VARCHAR(300),
  artist_id  UUID, album_id UUID, duration_sec INT,
  genre      VARCHAR(50), release_date DATE,
  audio_url  TEXT,            -- CDN URL
  stream_count BIGINT DEFAULT 0
);
```

### Playlists
```sql
CREATE TABLE playlists (
  playlist_id UUID PRIMARY KEY, owner_id UUID,
  name VARCHAR(200), is_public BOOLEAN, created_at TIMESTAMP
);
CREATE TABLE playlist_tracks (
  playlist_id UUID, song_id UUID, position INT, added_at TIMESTAMP,
  PRIMARY KEY (playlist_id, position)
);
```

---

## Recommendations Engine

### Discover Weekly (Weekly batch)
1. Collaborative filtering: "users similar to you liked..."
2. Content-based: audio features (tempo, key, energy) from ML models
3. Listen history + explicit likes/skips weighted
4. Generated Sunday midnight, delivered Monday morning

### Radio / Autoplay (Real-time)
- Seed track → find similar by audio feature vectors (cosine similarity)
- Served from pre-computed similarity index (Faiss/Annoy)

---

## Offline Download (Premium)

```
Client requests download → Encryption Service issues device-bound key
    ↓
Encrypted audio chunks stored on device
    ↓
Playback: decrypt in-memory only (DRM protected)
Keys expire: 30 days (must reconnect to refresh)
```

---

## Cross-Device Playback

- Active session tracked in Redis: `session:{user_id} → {device_id, song_id, position_ms}`
- User opens new device → fetches current session → resume from position
- WebSocket push from old device to new device for seamless handoff

---

## Social / Activity Feed

- Kafka event on each play
- Friend activity feed in Redis: `LPUSH activity:{user_id} event`
- "Friend is listening to X" shown in real-time on home screen

---

## Summary
Spotify = CDN-heavy audio streaming + ML-powered recommendations. Chunked audio with adaptive bitrate ensures smooth playback. Offline download with DRM protects content. The recommendation engine (collaborative + content-based) is the key engagement driver.

---

# Design a Live Video Streaming Platform

**Difficulty:** Medium

## Problem Statement
Design a live video streaming platform (like Twitch/YouTube Live) that ingests live streams, distributes them to thousands/millions of concurrent viewers globally.

---

## Functional Requirements
- Streamers broadcast live video
- Viewers watch with low latency (<30s for non-interactive, <3s for interactive)
- Live chat
- Stream discovery (browse, search)
- Recording and VOD (replay)
- Viewer count and analytics
- Subscriptions and donations

---

## Capacity Estimation

| Metric | Value |
|--------|-------|
| Concurrent streamers | 100K |
| Concurrent viewers | 10M |
| Avg stream bitrate | 5 Mbps |
| Ingest bandwidth | 100K × 5Mbps = 500 Gbps |
| CDN egress | 10M × 3Mbps = 30 Tbps |

---

## Architecture

```
Streamer (OBS/RTMP)
    ↓ RTMP
Ingest Servers (closest region)
    ↓
Transcoding Service (HLS segments: 360p, 720p, 1080p)
    ↓
Stream CDN (Edge nodes worldwide)
    ↓
Viewers (HLS playback)

Chat Service      → WebSocket → Redis Pub/Sub → viewers
Analytics Service → Kafka → ClickHouse
VOD Storage       → S3 (segments stored during live)
```

---

## Ingest to Playback Pipeline

```
1. Streamer sends RTMP to nearest ingest server
2. Ingest server relays to transcoding cluster
3. Transcoding: FFmpeg converts to HLS
   - One 2-second segment per quality level
   - Manifest (.m3u8) updated every 2s
4. Segments pushed to S3 + CDN edge nodes
5. Viewers download manifest → fetch segments
6. End-to-end latency: ~20-30s (standard HLS)
   For low-latency: LL-HLS or WebRTC (~2-5s)
```

---

## Protocol Comparison

| Protocol | Latency | Scale | Use Case |
|----------|---------|-------|----------|
| RTMP (ingest) | N/A | - | Streamer → server |
| HLS (playback) | 20-30s | Massive | Broadcast |
| LL-HLS | 2-5s | Large | Interactive streams |
| WebRTC | <1s | Medium | Ultra-interactive |

---

## Live Chat Architecture

- Viewers connect via WebSocket to Chat Service
- Messages published to Redis Pub/Sub channel: `chat:{stream_id}`
- All Chat Service instances subscribed → broadcast to connected viewers
- Rate limiting: max 10 messages/30s per user
- Moderation: ML spam filter + channel moderators

## Scale Challenge for Large Streams
- 100K+ concurrent chat users → millions of messages/min
- Solution: Chat sharding by stream_id, client-side message sampling for very popular streams

---

## Stream Discovery

```sql
CREATE TABLE streams (
  stream_id   UUID PRIMARY KEY, streamer_id UUID,
  title       VARCHAR(300), category VARCHAR(50),
  viewer_count INT DEFAULT 0,
  status      VARCHAR(20),   -- live, ended
  started_at  TIMESTAMP, ended_at TIMESTAMP
);
```

Browse: `ORDER BY viewer_count DESC WHERE status='live'`
Viewer count: updated every 30s from Redis `ZCARD viewers:{stream_id}`

---

## VOD / Recording

- During live stream: HLS segments saved to S3 in real-time
- On stream end: concatenate segments → full VOD file
- S3 lifecycle: hot for 30 days, cold storage after
- Subtitles: async speech-to-text after stream ends

---

## Summary
Live streaming = RTMP ingest → HLS transcode → CDN distribution. The key tradeoff is latency vs scale: HLS scales to millions but has 20-30s lag; LL-HLS/WebRTC gives <5s lag but requires more infrastructure. Live chat at scale requires Redis Pub/Sub + message sampling for viral streams.

---

## 🔴 Hard (37 Problems)

# Design Ticketmaster

**Difficulty:** Hard

---

## Problem Statement

Design a large-scale event ticketing platform like Ticketmaster that handles high-demand ticket sales, seat selection, and payment processing.

**Consider the following:**
- How would you handle millions of users simultaneously trying to purchase tickets for a popular event?
- How would you implement a fair virtual waiting queue?
- How would you prevent seat double-booking under high concurrency?
- How would you design the seat map and real-time availability display?
- How would you integrate with multiple payment gateways reliably?
- How would you prevent ticket scalping and bot purchases?

---

# Design Dropbox

**Difficulty:** Hard

---

## Problem Statement

Design a cloud file storage and synchronization service like Dropbox that allows users to store files and sync them across multiple devices.

**Consider the following:**
- How would you chunk, deduplicate, and upload large files efficiently?
- How would you detect and sync only the changed parts of a file (delta sync)?
- How would you handle conflict resolution when the same file is edited on two devices?
- How would you store file metadata separately from file content?
- How would you support file sharing and collaboration?
- How would you scale storage to exabytes of data?

---

# Design Uber Backend

**Difficulty:** Hard

---

## Problem Statement

Design the backend for a ride-hailing platform like Uber that matches riders with nearby drivers in real time.

**Consider the following:**
- How would you track the real-time location of millions of drivers?
- How would you efficiently find the nearest available drivers for a rider?
- How would you implement the matching algorithm considering ETA and driver acceptance?
- How would you handle the trip lifecycle (request, match, pickup, ride, payment)?
- How would you implement surge pricing based on supply and demand?
- How would you ensure reliability and handle driver/rider disconnections?

---

# Design Yelp or Nearby Friends

**Difficulty:** Hard

---

## Problem Statement

Design a location-based service like Yelp for discovering nearby businesses or a Nearby Friends feature that shows users which friends are close.

**Consider the following:**
- How would you efficiently query nearby places or users given a location and radius?
- Which geospatial indexing approach would you use (geohash, quadtree, S2)?
- How would you store and update real-time friend locations?
- How would you handle privacy settings for location sharing?
- How would you scale the proximity search to millions of concurrent users?
- How would you rank nearby results by relevance and distance?

---

# Design Facebook's Newsfeed

**Difficulty:** Hard

---

## Problem Statement

Design Facebook's News Feed system that aggregates and ranks content from friends, pages, and groups to display a personalized feed to each user.

**Consider the following:**
- How would you generate a personalized ranked feed for each of billions of users?
- How would you handle the fan-out problem for users with thousands of friends?
- How would you rank feed items using a relevance model?
- How would you balance content freshness vs relevance in ranking?
- How would you handle feed generation for both active and inactive users?
- How would you incorporate ads into the feed without degrading experience?

---

# Design a Web Crawler

**Difficulty:** Hard

---

## Problem Statement

Design a scalable web crawler that systematically browses the web, discovers new pages, and stores content for indexing by a search engine.

**Consider the following:**
- How would you schedule and prioritize which URLs to crawl next?
- How would you avoid crawling the same URL multiple times (URL deduplication)?
- How would you respect robots.txt and crawl rate limits per domain?
- How would you handle dynamic JavaScript-rendered pages?
- How would you distribute the crawler across thousands of machines?
- How would you detect and handle crawler traps (infinite URL spaces)?

---

# Design Google Map

**Difficulty:** Hard

---

## Problem Statement

Design a mapping and navigation platform like Google Maps that provides map rendering, routing, real-time traffic, and search for places.

**Consider the following:**
- How would you store and serve map tile data at various zoom levels globally?
- How would you implement efficient routing using road graph algorithms (Dijkstra, A*)?
- How would you incorporate real-time traffic data into routing?
- How would you support turn-by-turn navigation with rerouting?
- How would you handle searching for businesses and points of interest?
- How would you process and update the road network from crowdsourced data?

---

# Design Google Doc

**Difficulty:** Hard

---

## Problem Statement

Design a real-time collaborative document editing system like Google Docs where multiple users can simultaneously edit a document and see each other's changes.

**Consider the following:**
- How would you handle simultaneous edits from multiple users without conflicts?
- How would you implement Operational Transformation or CRDT-based conflict resolution?
- How would you persist document state and support revision history?
- How would you show cursor positions and presence of other editors?
- How would you sync changes reliably over unreliable network connections?
- How would you support rich text formatting, images, and comments?

---

# Design an Automated Trading Platform

**Difficulty:** Hard

---

## Problem Statement

Design a high-frequency automated trading platform that executes buy/sell orders based on algorithmic strategies with minimal latency.

**Consider the following:**
- How would you minimize end-to-end latency from market data to order execution?
- How would you ingest and process real-time market data feeds?
- How would you implement and backtest trading strategies?
- How would you manage risk limits and prevent runaway strategies?
- How would you ensure auditability and regulatory compliance of all trades?
- How would you handle partial fills, order cancellations, and market disruptions?

---

# Design a Key Value Store

**Difficulty:** Hard

---

## Problem Statement

Design a distributed key-value store similar to DynamoDB, Cassandra, or Redis that supports get, put, and delete operations with high availability and partition tolerance.

**Consider the following:**
- How would you distribute keys across nodes using consistent hashing?
- How would you handle replication for fault tolerance?
- How would you resolve conflicts between replicas (vector clocks, last-write-wins)?
- How would you handle node failures and data rebalancing?
- How would you implement a storage engine (LSM tree, B-tree)?
- What consistency model would you offer (eventual, strong, tunable)?

---

# Design a Global Content Distribution Network

**Difficulty:** Hard

---

## Problem Statement

Design a global CDN that caches and delivers static and dynamic content from edge servers close to users to reduce latency and origin load.

**Consider the following:**
- How would you determine which edge server to route a user's request to?
- How would you handle cache population, TTL, and invalidation across edges?
- How would you support both static asset caching and dynamic content acceleration?
- How would you handle failover when an edge node goes down?
- How would you implement SSL termination and DDoS protection at the edge?
- How would you measure and optimize cache hit rates globally?

---

# Design a Peer-to-Peer Network

**Difficulty:** Hard

---

## Problem Statement

Design a peer-to-peer network for decentralized file sharing or communication where nodes discover each other and exchange data without a central server.

**Consider the following:**
- How would you implement peer discovery (DHT, bootstrap nodes)?
- How would you route requests across the peer network efficiently?
- How would you handle peers joining and leaving the network (churn)?
- How would you distribute and replicate data across peers?
- How would you verify data integrity in a trustless environment?
- How would you handle NAT traversal for peers behind firewalls?

---

# Design a Distributed Messaging System

**Difficulty:** Hard

---

## Problem Statement

Design a distributed messaging system like Apache Kafka that supports high-throughput, fault-tolerant, ordered message streaming between producers and consumers.

**Consider the following:**
- How would you partition topics across brokers for parallelism?
- How would you ensure message durability through replication?
- How would you maintain message ordering within a partition?
- How would you handle consumer group offset management?
- How would you recover from broker failures without message loss?
- How would you scale to millions of messages per second?

---

# Design a Distributed File System

**Difficulty:** Hard

---

## Problem Statement

Design a distributed file system like HDFS or GFS that stores large files across a cluster of machines with fault tolerance and high throughput.

**Consider the following:**
- How would you split large files into chunks and distribute them across nodes?
- How would you replicate chunks to tolerate node failures?
- How would you implement a metadata service (NameNode) to track file locations?
- How would you handle concurrent reads and writes to the same file?
- How would you recover from data node failures and re-replicate lost chunks?
- How would you support sequential read/write patterns optimized for large files?

---

# Design a Log Collection and Analysis System

**Difficulty:** Hard

---

## Problem Statement

Design a centralized log collection and analysis platform like the ELK Stack (Elasticsearch, Logstash, Kibana) that aggregates logs from thousands of services.

**Consider the following:**
- How would you collect logs from thousands of distributed services with minimal overhead?
- How would you handle log ingestion spikes without losing data?
- How would you parse, enrich, and normalize logs from different formats?
- How would you store and index logs for fast full-text search?
- How would you implement real-time alerting based on log patterns?
- How would you manage log retention and archival policies?

---

# Design a Load Balancer

**Difficulty:** Hard

---

## Problem Statement

Design a software load balancer that distributes incoming network traffic across a pool of backend servers to ensure availability and performance.

**Consider the following:**
- Which load balancing algorithms would you support (round robin, least connections, IP hash)?
- How would you perform health checks and remove unhealthy backends?
- How would you implement session persistence (sticky sessions)?
- How would you handle SSL termination at the load balancer?
- How would you make the load balancer itself highly available?
- How would you support dynamic backend registration and deregistration?

---

# Design a Scalable Email Service

**Difficulty:** Hard

---

## Problem Statement

Design a scalable email sending and receiving service like Gmail or SendGrid that handles billions of emails reliably.

**Consider the following:**
- How would you handle high-volume outbound email delivery?
- How would you manage sender reputation and deliverability (SPF, DKIM, DMARC)?
- How would you implement inbound email processing and storage?
- How would you handle bounce processing and unsubscribes?
- How would you design the inbox search and retrieval system?
- How would you prevent spam and abuse of the sending platform?

---

# Design a Food Delivery Service

**Difficulty:** Hard

---

## Problem Statement

Design an on-demand food delivery platform like DoorDash or Uber Eats that connects customers, restaurants, and delivery drivers.

**Consider the following:**
- How would you match incoming orders with available nearby drivers?
- How would you estimate and display accurate delivery ETAs?
- How would you handle restaurant order management and kitchen display?
- How would you optimize delivery routing for drivers with multiple orders?
- How would you manage restaurant menus and real-time item availability?
- How would you handle the full order lifecycle from placement to delivery confirmation?

---

# Design a Hotel Booking Service

**Difficulty:** Hard

---

## Problem Statement

Design a hotel booking platform like Booking.com that allows users to search for hotels by location and date, view availability, and complete reservations.

**Consider the following:**
- How would you model room types, rates, and availability calendars?
- How would you handle concurrent booking requests for the last available room?
- How would you search and rank hotels by location, price, and filters?
- How would you implement a reservation hold and payment flow?
- How would you sync availability with hotel property management systems?
- How would you handle cancellations and refund policies?

---

# Design an Auction System

**Difficulty:** Hard

---

## Problem Statement

Design an online auction system like eBay where users can list items, place bids, and win items through timed auctions.

**Consider the following:**
- How would you handle concurrent bid submissions and determine the winner?
- How would you implement auto-bidding (proxy bidding) on behalf of users?
- How would you manage auction timers and handle sniping near end time?
- How would you notify all bidders of new bids in real time?
- How would you process payment upon auction close?
- How would you scale to millions of simultaneous active auctions?

---

# Design a Chatbot Framework

**Difficulty:** Hard

---

## Problem Statement

Design a chatbot development framework that allows businesses to build, deploy, and manage AI-powered conversational agents across multiple channels.

**Consider the following:**
- How would you design the NLU (intent detection, entity extraction) pipeline?
- How would you manage multi-turn conversation context and state?
- How would you support deployment across channels (web, WhatsApp, SMS, voice)?
- How would you provide a dialog authoring tool for non-engineers?
- How would you handle fallback and human handoff gracefully?
- How would you monitor chatbot performance and collect training data?

---

# Design a Push Notification Service

**Difficulty:** Hard

---

## Problem Statement

Design a push notification service that delivers real-time notifications to mobile and web clients at scale, similar to Firebase Cloud Messaging.

**Consider the following:**
- How would you route notifications to the correct platform (iOS APNs, Android FCM, Web Push)?
- How would you handle device token management and token rotation?
- How would you ensure delivery reliability and handle failures?
- How would you support scheduled and targeted notifications?
- How would you scale delivery to billions of devices?
- How would you track notification delivery and open rates?

---

# Design a Smart Home System

**Difficulty:** Hard

---

## Problem Statement

Design a smart home platform that connects and controls IoT devices (lights, thermostats, locks, cameras) through a central hub and mobile app.

**Consider the following:**
- How would you handle device discovery and registration?
- How would you communicate with devices using different protocols (Zigbee, Z-Wave, WiFi, MQTT)?
- How would you implement real-time device control and status monitoring?
- How would you process data from sensors and cameras at the edge?
- How would you design automation rules (if-this-then-that)?
- How would you ensure security and prevent unauthorized device access?

---

# Design a Secure Identity Management System

**Difficulty:** Hard

---

## Problem Statement

Design a secure identity and access management (IAM) system that handles user authentication, authorization, and single sign-on across services.

**Consider the following:**
- How would you implement secure password hashing and storage?
- How would you support multi-factor authentication (TOTP, SMS, biometric)?
- How would you implement OAuth 2.0 and OpenID Connect for SSO?
- How would you manage roles, permissions, and policy enforcement?
- How would you detect and respond to suspicious login activity?
- How would you handle credential rotation and session management?

---

# Design a Real Time Stock Trading Platform

**Difficulty:** Hard

---

## Problem Statement

Design a real-time stock trading platform that allows users to view live market data, place orders, and manage their portfolio.

**Consider the following:**
- How would you ingest and distribute real-time market data feeds to users?
- How would you implement an order matching engine?
- How would you handle different order types (market, limit, stop-loss)?
- How would you maintain portfolio positions and calculate P&L in real time?
- How would you ensure transactional integrity for order execution?
- How would you implement circuit breakers for extreme market volatility?

---

# Design Google Search

**Difficulty:** Hard

---

## Problem Statement

Design a web-scale search engine like Google that can index billions of web pages and return highly relevant results in milliseconds.

**Consider the following:**
- How would you crawl and index billions of web pages?
- How would you build an inverted index for full-text search?
- How would you rank results using signals like PageRank and relevance?
- How would you serve queries with sub-100ms latency at massive scale?
- How would you handle query understanding (spelling correction, synonyms, intent)?
- How would you keep the index fresh as web content changes?

---

# Design a Scheduled Digital Transaction System

**Difficulty:** Hard

---

## Problem Statement

Design a system that reliably executes scheduled financial transactions (recurring payments, direct deposits, standing orders) at specified times.

**Consider the following:**
- How would you store and trigger transactions at precise scheduled times?
- How would you ensure each scheduled transaction is executed exactly once?
- How would you handle failures and retry logic for failed transactions?
- How would you manage schedule changes and cancellations?
- How would you scale to millions of scheduled transactions per day?
- How would you audit and report on all scheduled transaction executions?

---

# Design a Distributed Linked List

**Difficulty:** Hard

---

## Problem Statement

Design a distributed linked list data structure that partitions list nodes across multiple machines while supporting traversal, insertion, and deletion.

**Consider the following:**
- How would you partition nodes across machines while maintaining logical ordering?
- How would you traverse the list efficiently across distributed nodes?
- How would you handle concurrent insertions and deletions?
- How would you maintain consistency across replicated nodes?
- How would you handle machine failures without losing list integrity?
- What are the performance trade-offs compared to a centralized linked list?

---

# Design a Distributed Tracing System

**Difficulty:** Hard

---

## Problem Statement

Design a distributed tracing system like Jaeger or Zipkin that tracks requests as they flow through microservices to diagnose latency and failures.

**Consider the following:**
- How would you propagate trace context across service boundaries?
- How would you collect spans from thousands of services with minimal overhead?
- How would you store and index trace data for fast retrieval?
- How would you visualize end-to-end request traces with timing breakdown?
- How would you handle sampling to reduce data volume?
- How would you correlate traces with logs and metrics?

---

# Design a Wide Column Database

**Difficulty:** Hard

---

## Problem Statement

Design a wide column store database like Apache Cassandra or Google Bigtable that supports high write throughput and flexible schemas at petabyte scale.

**Consider the following:**
- How would you design the data model with partition keys and clustering columns?
- How would you implement an LSM-tree based storage engine?
- How would you distribute and replicate data across a cluster?
- How would you handle read and write consistency levels?
- How would you implement compaction to reclaim space from deleted data?
- How would you handle schema evolution without downtime?

---

# Design a Metrics Monitoring and Alerting System

**Difficulty:** Hard

---

## Problem Statement

Design a metrics collection, storage, and alerting system like Prometheus + Grafana that monitors the health of distributed services.

**Consider the following:**
- How would you collect metrics from thousands of services via pull or push?
- How would you store time-series metrics data efficiently (TSDB)?
- How would you implement a query language for metrics aggregation?
- How would you define and evaluate alerting rules?
- How would you send alert notifications through multiple channels (PagerDuty, Slack, email)?
- How would you handle high cardinality in metric labels?

---

# Design a Graph Search Function for a Social Network

**Difficulty:** Hard

---

## Problem Statement

Design a graph search feature for a social network like Facebook's Graph Search that lets users query their social graph with natural language (e.g., 'friends who like hiking').

**Consider the following:**
- How would you model the social graph at scale (billions of nodes, edges)?
- How would you index node attributes for efficient filtered graph queries?
- How would you execute graph traversal queries efficiently?
- How would you rank results by social relevance and privacy settings?
- How would you handle schema changes as new entity types are added?
- How would you scale query execution across a distributed graph?

---

# Design a Resource Allocation Service

**Difficulty:** Hard

---

## Problem Statement

Design a service that optimally allocates limited resources (compute, bandwidth, seats, appointments) across competing requests.

**Consider the following:**
- How would you model the resource pool and allocation constraints?
- How would you handle concurrent allocation requests without conflicts?
- How would you implement priority-based allocation for different tiers?
- How would you reclaim resources from expired or abandoned allocations?
- How would you scale allocation logic across a distributed system?
- How would you handle fairness and prevent starvation for lower-priority requesters?

---

# Design an Event Lifecycle Management System

**Difficulty:** Hard

---

## Problem Statement

Design a system that manages the full lifecycle of events (conferences, concerts, meetups) from creation and ticketing through check-in and post-event analytics.

**Consider the following:**
- How would you model events with schedules, sessions, and speakers?
- How would you handle ticket sales with capacity limits?
- How would you implement check-in validation at event entry?
- How would you push real-time updates to attendees (schedule changes, announcements)?
- How would you collect and aggregate post-event feedback?
- How would you generate event analytics for organizers?

---

# Design a Web Crawler That Will Crawl Wikipedia

**Difficulty:** Hard

---

## Problem Statement

Design a focused web crawler specifically built to crawl all of Wikipedia, extract article content and inter-article links, and keep the data fresh.

**Consider the following:**
- How would you seed the crawler and manage the frontier of Wikipedia URLs?
- How would you parse wikitext or HTML to extract clean article content and links?
- How would you handle Wikipedia's link graph without infinite loops?
- How would you respect Wikipedia's rate limits and API policies?
- How would you detect article updates and re-crawl changed pages?
- How would you store the extracted content for downstream indexing or analysis?

---

# Design an Ad Click Aggregation System

**Difficulty:** Hard

---

## Problem Statement

Design a system that aggregates ad click events in real time and provides accurate reporting for advertisers on clicks, impressions, and conversions.

**Consider the following:**
- How would you ingest billions of ad click events per day?
- How would you deduplicate clicks to prevent fraudulent billing?
- How would you aggregate click data into per-campaign, per-ad metrics in near real time?
- How would you reconcile real-time and batch aggregation results?
- How would you store aggregated data for fast dashboard queries?
- How would you ensure accuracy under high throughput with late-arriving events?

---

# Design a Service to Allocate Pool of Resources Optimally

**Difficulty:** Hard

---

## Problem Statement

Design a service that manages a shared pool of resources (e.g., cloud VMs, database connections, API quota) and allocates them optimally to competing clients.

**Consider the following:**
- How would you track resource availability and current allocations in real time?
- How would you implement allocation policies (round robin, weighted, priority)?
- How would you handle resource requests that exceed current availability?
- How would you support time-bound leases and automatic reclamation?
- How would you scale the allocation service to handle thousands of concurrent requests?
- How would you prevent resource starvation for lower-priority clients?

---

## 🔵 Advanced (16 Problems)

# Design a Cloud Infrastructure for Tech Enterprises

**Difficulty:** Advanced

---

## Problem Statement

Design a multi-tenant cloud infrastructure platform like AWS or Azure that provides compute, storage, networking, and managed services to enterprise customers.

**Consider the following:**
- How would you design the virtualization layer for compute resources?
- How would you implement multi-tenancy with strong resource and security isolation?
- How would you manage the global network backbone and data center interconnects?
- How would you design the control plane for resource provisioning and management?
- How would you implement billing, metering, and cost allocation?
- How would you achieve five-nines availability across the platform?

---

# Design a Container Orchestration System

**Difficulty:** Advanced

---

## Problem Statement

Design a container orchestration platform like Kubernetes that automates deployment, scaling, and management of containerized applications.

**Consider the following:**
- How would you design the control plane (scheduler, controller manager, API server)?
- How would you schedule pods onto nodes based on resource requirements?
- How would you implement self-healing (restart failed containers, reschedule evicted pods)?
- How would you manage service discovery and load balancing between pods?
- How would you handle rolling deployments and rollbacks?
- How would you implement resource quotas and limits per namespace?

---

# Design a High-Performance Computing Cluster

**Difficulty:** Advanced

---

## Problem Statement

Design a high-performance computing (HPC) cluster for running large-scale scientific simulations, machine learning training, or data processing workloads.

**Consider the following:**
- How would you design the high-speed interconnect fabric (InfiniBand, RDMA)?
- How would you implement a job scheduler for batch workloads (SLURM-like)?
- How would you provide a parallel shared file system (Lustre, GPFS)?
- How would you manage GPU clusters for deep learning workloads?
- How would you handle fault tolerance in a cluster of thousands of nodes?
- How would you monitor performance and detect node failures?

---

# Design a Cloud Based Data Backup Solution

**Difficulty:** Advanced

---

## Problem Statement

Design a cloud-based backup service that allows organizations to back up and restore on-premises data and cloud workloads reliably and cost-efficiently.

**Consider the following:**
- How would you perform incremental backups to minimize data transfer?
- How would you deduplicate and compress backup data for storage efficiency?
- How would you design the restore process to minimize RTO (recovery time objective)?
- How would you encrypt backup data in transit and at rest?
- How would you manage backup retention policies and lifecycle tiering?
- How would you test backup integrity and ensure recoverability?

---

# Design a Cloud Storage Gateway

**Difficulty:** Advanced

---

## Problem Statement

Design a cloud storage gateway that bridges on-premises applications to cloud storage (S3, Azure Blob, GCS) while providing local caching and protocol translation.

**Consider the following:**
- How would you cache frequently accessed data locally to reduce cloud access latency?
- How would you translate on-premises protocols (NFS, SMB, iSCSI) to cloud storage APIs?
- How would you handle cache eviction and consistency with cloud storage?
- How would you ensure data durability during network outages?
- How would you optimize data tiering between local and cloud storage?
- How would you manage encryption for data in transit and at rest?

---

# Design a Virtualization System

**Difficulty:** Advanced

---

## Problem Statement

Design a server virtualization platform like VMware vSphere or KVM that allows multiple virtual machines to run on shared physical hardware.

**Consider the following:**
- How would you implement the hypervisor layer (Type-1 or Type-2)?
- How would you virtualize CPU, memory, storage, and network resources?
- How would you implement live VM migration between physical hosts?
- How would you provide strong isolation between VMs on the same host?
- How would you manage VM snapshots and cloning?
- How would you schedule VM resource allocation across a cluster of hosts?

---

# Design a Disaster Recovery System

**Difficulty:** Advanced

---

## Problem Statement

Design a disaster recovery (DR) system that ensures business continuity by replicating critical systems and data to a secondary site and enabling rapid failover.

**Consider the following:**
- How would you replicate data continuously to the DR site with minimal RPO (recovery point objective)?
- How would you design the failover process to minimize RTO?
- How would you test DR failover without impacting production?
- How would you handle partial failures and gradual degradation?
- How would you manage DNS and traffic cutover during failover?
- How would you define and enforce recovery objectives across different service tiers?

---

# Design a Hybrid Cloud Infrastructure

**Difficulty:** Advanced

---

## Problem Statement

Design a hybrid cloud architecture that seamlessly connects on-premises data centers with public cloud resources for workload portability and burst capacity.

**Consider the following:**
- How would you design the secure network connectivity between on-premises and cloud (VPN, Direct Connect)?
- How would you enable workload portability using containers or VMs?
- How would you implement unified identity and access management across environments?
- How would you manage data gravity and minimize data transfer costs?
- How would you implement a unified monitoring and observability plane?
- How would you design burst-to-cloud policies for peak demand handling?

---

# Design a Cloud Service Brokerage Platform

**Difficulty:** Advanced

---

## Problem Statement

Design a cloud service brokerage (CSB) platform that aggregates services from multiple cloud providers and allows enterprises to procure, manage, and govern cloud services through a single interface.

**Consider the following:**
- How would you abstract and normalize APIs across different cloud providers?
- How would you implement service catalog management and self-service provisioning?
- How would you handle billing aggregation and cost optimization across providers?
- How would you enforce governance policies and compliance across cloud services?
- How would you manage identity federation across multiple cloud environments?
- How would you implement a unified monitoring dashboard for multi-cloud resources?

---

# Design a Serverless Architecture Framework

**Difficulty:** Advanced

---

## Problem Statement

Design a serverless computing platform like AWS Lambda or Azure Functions that automatically executes code in response to events without users managing servers.

**Consider the following:**
- How would you implement cold start optimization for function invocation?
- How would you isolate function executions securely using sandboxing (gVisor, Firecracker)?
- How would you implement event-driven triggers (HTTP, queue, cron, stream)?
- How would you auto-scale functions to zero and scale up on demand?
- How would you manage function dependencies and deployment packages?
- How would you implement function composition and orchestration?

---

# Design a Large-Scale Graph Processing System

**Difficulty:** Advanced

---

## Problem Statement

Design a distributed graph processing system like Apache Giraph or Google Pregel that can execute graph algorithms (PageRank, BFS, community detection) on graphs with billions of nodes.

**Consider the following:**
- How would you partition a large graph across a cluster of machines?
- How would you implement the vertex-centric (think like a vertex) computation model?
- How would you handle iterative graph algorithms with message passing?
- How would you minimize communication overhead between partitions?
- How would you handle skewed graphs with super-nodes (high-degree vertices)?
- How would you support fault tolerance and checkpointing during long computations?

---

# Design a Big Data Processing Pipeline

**Difficulty:** Advanced

---

## Problem Statement

Design a big data pipeline like the Lambda or Kappa architecture that ingests, processes, and serves large volumes of data for both real-time and batch analytics.

**Consider the following:**
- How would you design the ingestion layer to handle high-velocity data streams?
- How would you implement both stream processing (Flink, Spark Streaming) and batch processing?
- How would you design the serving layer for fast analytical queries?
- How would you handle schema evolution and late-arriving data?
- How would you ensure data quality and lineage tracking?
- How would you manage pipeline orchestration, monitoring, and failure recovery?

---

# Design a Virtual Reality Streaming Service

**Difficulty:** Advanced

---

## Problem Statement

Design a cloud-based VR streaming service that renders high-fidelity VR content on remote servers and streams it to lightweight headsets with ultra-low latency.

**Consider the following:**
- How would you achieve the ultra-low latency (< 20ms) required for comfortable VR?
- How would you stream high-resolution, high-framerate 360-degree video efficiently?
- How would you handle head movement prediction to reduce perceived latency?
- How would you implement server-side rendering farms for VR workloads?
- How would you adapt streaming quality to network conditions dynamically?
- How would you design the edge computing infrastructure for global low-latency delivery?

---

# Design a Network Security Monitoring Tool

**Difficulty:** Advanced

---

## Problem Statement

Design a network security monitoring (NSM) platform that captures, analyzes, and alerts on suspicious network traffic across an enterprise.

**Consider the following:**
- How would you capture and process high-throughput network traffic at wire speed?
- How would you implement intrusion detection using signature and anomaly-based methods?
- How would you correlate security events across multiple network sensors?
- How would you store and query large volumes of network flow data?
- How would you implement real-time alerting with low false-positive rates?
- How would you handle encrypted traffic analysis without breaking TLS?

---

# Design a Distributed OLTP Database

**Difficulty:** Advanced

---

## Problem Statement

Design a distributed OLTP (Online Transaction Processing) database like CockroachDB or Google Spanner that supports ACID transactions across a globally distributed cluster.

**Consider the following:**
- How would you implement distributed ACID transactions with serializability?
- How would you use consensus algorithms (Raft, Paxos) for replication?
- How would you implement a globally consistent timestamp for transactions (TrueTime-like)?
- How would you partition and rebalance data across nodes as the cluster grows?
- How would you handle cross-shard transactions efficiently?
- How would you provide strong consistency while minimizing cross-region latency?

---

# Design a Domain Name System

**Difficulty:** Advanced

---

## Problem Statement

Design the Domain Name System (DNS) infrastructure that resolves human-readable domain names to IP addresses at global scale.

**Consider the following:**
- How would you design the hierarchical DNS resolution process (recursive resolver, TLD, authoritative)?
- How would you implement caching at multiple levels to reduce resolution latency?
- How would you handle DNS record types (A, AAAA, CNAME, MX, TXT, NS)?
- How would you distribute authoritative DNS servers globally with anycast routing?
- How would you implement DNSSEC for response validation?
- How would you handle DDoS attacks against DNS infrastructure?

---

