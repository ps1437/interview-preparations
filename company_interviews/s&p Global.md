## 1. System Design: Flight Reservation System
- **Users**: Admin, Airline Staff, Customers
- **Core Modules**: 
  - Flight Management
  - Seat Inventory
  - Booking & Payments
  - Notification (Email/SMS)
  - Search Engine
  - Cancellations & Refunds
- **Tech Stack**: 
  - Backend: Spring Boot (REST APIs)
  - Frontend: React
  - Database: SQL for bookings, NoSQL for search cache
  - Queue: Kafka for async processing
  - Cache: Redis for search results

---

## 2. NoSQL vs SQL
| Feature         | SQL (RDBMS)                | NoSQL                          |
|----------------|----------------------------|--------------------------------|
| Schema         | Fixed schema               | Dynamic schema                 |
| Joins          | Supported                  | Not supported (mostly)         |
| Use-case       | Transactions, Relations    | Big Data, Flexible data        |
| Examples       | MySQL, PostgreSQL          | MongoDB, Cassandra             |

---

## 3. CAP Theorem
- **C**onsistency: All nodes see same data.
- **A**vailability: Every request gets a response.
- **P**artition Tolerance: System continues despite network split.

---

## 4. AP / CP / CA Databases
| Type | Examples        | Characteristics                   |
|------|------------------|------------------------------------|
| CP   | MongoDB, HBase   | Consistency + Partition Tolerance |
| AP   | Cassandra, DynamoDB | Availability + Partition Tolerance |
| CA   | Traditional RDBMS (without partition) | Consistency + Availability |

---

## 5. Spring Boot vs Spring
| Feature       | Spring              | Spring Boot                    |
|---------------|---------------------|--------------------------------|
| Setup         | Manual configuration| Auto-configuration             |
| Deployment    | WAR (mostly)        | Embedded server (JAR)         |
| Focus         | Framework           | Rapid development & microservices|

---

## 6. Richardson Maturity Model (RMM)
- **Level 0**: Single URI, POST only
- **Level 1**: Resources (URIs)
- **Level 2**: HTTP Verbs (GET, POST, PUT, DELETE)
- **Level 3**: HATEOAS (Hypermedia links in response)

---

## 7. HATEOAS and Use Case
- **HATEOAS**: Hypermedia as the Engine of Application State.
- **Use Case**: Guides client via links (e.g., next, cancel, etc.).
  Example: REST API returns available actions with each response.

---

## 8. React Hooks and Rules
- **Common Hooks**: `useState`, `useEffect`, `useContext`, `useReducer`
- **Rules**:
  - Call hooks at top-level only
  - Don’t call hooks conditionally
  - Only use inside functional components

---

## 9. Redux vs Context API
| Feature       | Redux                   | Context API                |
|---------------|--------------------------|----------------------------|
| Usage         | Global state + logic     | Lightweight global state   |
| Boilerplate   | More                     | Less                       |
| Async Handling| Thunks, sagas            | Not built-in               |
| Best Use Case | Large apps               | Small to medium apps       |

---

## 10. Project Architecture (Microservices)
- **Gateway**: API Gateway for routing/auth
- **Service Layer**: Auth, Flight, Booking, Notification
- **DB per service**
- **Inter-service**: REST/Kafka
- **Infra**: Docker, Kubernetes, Redis, Kafka

---

## 11. Kafka Idempotency & Components
- **Idempotency**: Avoid duplicate effects
- **How**: 
  - Use `enable.idempotence=true` for producers
  - Deduplication logic on consumer side
- **Components**: Producer, Consumer, Broker, Topic, Partition

---

## 12. Caching Strategies
| Strategy       | Description                                | Use Case                              |
|----------------|--------------------------------------------|----------------------------------------|
| Read Through   | Read from cache, miss -> load from DB      | High read apps like product catalog   |
| Write Through  | Write to cache + DB simultaneously         | Real-time sync                         |
| Write Behind   | Write to cache, DB update async            | High write throughput (flight prices) |

---

## 13. DB Design: Flight Reservation System

**Tables**:
- `users` (id, name, email, role)
- `airlines` (id, name, code)
- `flights` (id, airline_id, origin, destination, departure_time, arrival_time)
- `seats` (id, flight_id, seat_no, class, status)
- `bookings` (id, user_id, flight_id, status, booking_time)
- `payments` (id, booking_id, amount, status, payment_method)
- `notifications` (id, user_id, type, message)

Indexes on:
- `flight_id`, `user_id`
- `departure_time`, `status`

---

## 14. Immutable Class (Java)
**How to achieve**:
- Make class `final`
- Make fields `private final`
- No setters
- Defensive copy in constructors/getters
- Use builder if needed

**Kafka API tip**:
- Use immutable DTOs as Kafka event payloads to prevent side-effects across services.

