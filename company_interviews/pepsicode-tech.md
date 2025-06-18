## Pepsico interview experiance for Senior Assoicte Enginner Manager rol

---

## ✅ **1. Design Recommendation System for OTT Platform**

### 🧠 Goal:

Suggest personalized content to users based on behavior and preferences.

### 📐 High-Level Components:

* **User Service** (profiles, preferences)
* **Content Metadata Service** (genres, ratings, actors, tags)
* **Behavior Tracking Service** (watch history, likes, clicks)
* **Recommendation Engine**
* **Search & Ranking Service**
* **Cache Layer (Redis/ElasticSearch)**
* **Storage**: SQL for metadata, NoSQL for user activity

### 🔧 Techniques:

* **Collaborative Filtering** (User-Based / Item-Based)
* **Content-Based Filtering**
* **Hybrid (combine both)**
* Use **ML models** with **batch** + **real-time streaming (Kafka + Flink/Spark Streaming)**

---

## ✅ **2. Project Architecture (Microservices Based OTT App)**

```
[ Client (Web/Mobile) ]
       |
[ API Gateway / BFF ]
       |
[ Auth Service | User Service | Content Service | Recommendation Engine | Analytics Service ]
       |
[ MongoDB | Redis | Kafka | ElasticSearch | ML Pipeline | S3 | RDBMS ]
```

### 🔌 External:

* Vendor integrations for payment, ads, notifications.

### 🔐 Internal:

* Services communicate over REST/gRPC
* Use **Service Discovery (Eureka/Consul)**, **Circuit Breaker (Resilience4j)**, **Rate Limiting**

---

## ✅ **3. SQL vs NoSQL**

| Feature        | SQL (RDBMS)               | NoSQL (MongoDB, Cassandra)         |
| -------------- | ------------------------- | ---------------------------------- |
| Data Structure | Structured (tables)       | Flexible (JSON, key-value, column) |
| Scalability    | Vertical                  | Horizontal                         |
| ACID           | Strong                    | Eventual consistency (BASE)        |
| Use Case       | Relational, transactional | Unstructured, scalable, fast read  |

👉 In OTT:

* SQL → user profiles, subscriptions
* NoSQL → viewing history, recommendation metadata

---

## ✅ **4. Feature Flag Based on Role**

### 🔧 Backend:

```java
if (user.hasRole("ADMIN") && featureService.isEnabled("NEW_UI")) {
    showNewUI();
}
```

* Store flags in **DB, config server, or LaunchDarkly/Unleash**
* Use **ThreadLocal** to propagate user context

### ⚙ UI:

* Angular/React: Hide/show based on `user.role` & feature check from backend config

---

## ✅ **5. Security (JWT/OAuth2/Role-Based)**

### 🔐 Authentication:

* Use **OAuth2 + JWT** tokens via Auth0/Keycloak
* Token includes `userId`, `role`, `scopes`

### 🛡 Authorization:

* Method level: `@PreAuthorize("hasRole('ADMIN')")`
* Resource-based: check access before showing content

### 🔄 Real-Time:

* WebSockets/SSE secured with JWT
* Token refreshed using silent authentication (PKCE + refresh tokens)

---

## ✅ **6. BFF vs Aggregator Pattern**

| Aspect      | BFF (Backend For Frontend)         | Aggregator                       |
| ----------- | ---------------------------------- | -------------------------------- |
| Purpose     | Tailored backend for each frontend | Combine multiple services        |
| Use Case    | Mobile vs Web needs                | API Gateway or Composition Layer |
| Flexibility | High                               | Moderate                         |

➡ OTT: Use **BFF** to customize UI for TV vs mobile.

---

## ✅ **7. Components of Project**

* **API Gateway**: Authentication, routing, rate limiting
* **Load Balancer**: Distributes traffic (e.g., AWS ALB)
* **Internal Applications**: Auth, Recommendations, Watch History
* **External Services**: Notification, Payments, Analytics
* **Vendor Apps**: Ad services, Payment processors

---

## ✅ **8. Optimized Performance of an Application**

### 🔧 Techniques:

* **Caching**: Redis for token/user/profile
* **DB Indexing**: On frequently queried fields
* **Connection Pooling**: HikariCP
* **Thread Optimization**: WebFlux or CompletableFutures
* **GC tuning, profiling**: JFR, JMC, VisualVM
* **Lazy loading & pagination** for large lists

---

## ✅ **9. What is Idempotency?**

> **Idempotent Operation** = Repeated execution has the same effect as one execution.

### Example:

* **PUT /user/123**: Same data → safe to retry
* **POST /pay**: Needs idempotency key to avoid double charge

➡ Used in **payments, updates, retry logic**

---

## ✅ **10. How Kafka Ensures Semantic Consistency**

### Kafka Guarantees:

* **At Least Once** (default)
* **At Most Once**
* **Exactly Once** (EOI - using idempotent producers + transactional writes)

### Consistency in Kafka:

* **Message ordering** within a partition
* **Offset tracking** (committed manually or auto)
* **Compaction**: Maintains latest value for a key

➡ Use `acks=all`, `min.insync.replicas`, and **Kafka Streams/Connect** with idempotent writes.

---

## ✅ **11. Idempotency in Kafka**

### Kafka Producer:

```java
props.put("enable.idempotence", "true");
```

* Avoids duplication on retries
* Uses producer ID and sequence number

### Kafka Consumer:

* Store processed event keys (deduplication)
* Use transactional writes (`consumer -> db` with idempotent key check)

➡ Idempotency ensures **exactly-once behavior** when retries or crashes occur.


## Guess the OP

```java
List<String> names = List.of("Alice", "Bob", "Charlie");
 
names.stream()
     .map(name -> {
         System.out.println("Mapping: " + name);
         return name.toUpperCase();
     })
     .limit(1)
     .forEach(System.out::println);
```
What is the output and how many times is map() executed?



```java
@Service

public class OrderService {
 
    @Transactional
    public void placeOrder() {

        saveOrder(); // transactional

        throw new RuntimeException("Simulate failure");

    }
 
    public void saveOrder() {

        // DB save logic

        System.out.println("Order saved.");

    }

}
```
 Will the DB changes in saveOrder() be rolled back?

 

