# ⚙️ System Design Concepts – Cheat Sheet

Quick reference for key backend system properties and caching strategies.

---

## 🚀 Core System Properties

| Term             | Description                                                                                                                              |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| **Scalability**  | The system's ability to handle increased load (users, data, traffic) by upgrading resources (vertical) or adding instances (horizontal). |
| **Availability** | Percentage of time the system is operational and accessible (e.g., 99.99%).                                                              |
| **Latency**      | Time taken to process a single request (usually in ms). Lower is better.                                                                 |
| **Throughput**   | Number of requests handled per unit time (e.g., req/sec). Higher is better.                                                              |

---

## 🧠 Caching Strategies

### 1. **Write-Through Cache**

| Feature      | Description                                                                              |
| ------------ | ---------------------------------------------------------------------------------------- |
| How it works | Data is written to both cache and DB simultaneously.                                     |
| Cache Miss   | Data always available since written first to cache.                                      |
| Consistency  | High (cache always in sync with DB).                                                     |
| Latency      | Slightly higher on writes (due to dual writes).                                          |
| Use Case     | Read-heavy apps where strong consistency is needed (e.g., session store, user settings). |

```text
Client → Cache → DB
         ↑
     Write happens to both
```

---

### 2. **Read-Through Cache**

| Feature      | Description                                                                                         |
| ------------ | --------------------------------------------------------------------------------------------------- |
| How it works | Data is read through the cache layer. On cache miss, data is fetched from DB and loaded into cache. |
| Cache Miss   | Triggers DB call → stores result in cache.                                                          |
| Consistency  | Eventual (may return stale data if DB changes aren't reflected immediately).                        |
| Latency      | Fast on repeated reads (after warm-up).                                                             |
| Use Case     | Apps with frequent reads of slowly changing data (e.g., product catalogs).                          |

```text
Client → Cache → (miss) → DB → Cache ← Return
```

---

## 🆚 Read-Through vs Write-Through Cache

| Aspect         | Read-Through                   | Write-Through                     |
| -------------- | ------------------------------ | --------------------------------- |
| Data Loading   | On demand (lazy load)          | Proactively written during writes |
| Write Path     | DB only                        | Cache + DB                        |
| Read Miss Cost | Higher (needs DB call)         | Low (already cached)              |
| Consistency    | Eventual                       | Strong                            |
| Failure Risk   | Cache miss can degrade latency | Write failures may cause issues   |

---

## ⚠️ Bonus: Write-Behind (Async Write)

* Data is written to cache **first**, then persisted to DB **later asynchronously**.
* Pros: Fastest writes, ideal for high-throughput.
* Cons: Risk of data loss on failure, eventual consistency.
* Used in **analytics** or **buffered writes** scenarios.

---

## 🧪 Quick Decision Guide

| Scenario                       | Preferred Strategy   |
| ------------------------------ | -------------------- |
| Real-time data updates needed  | Write-Through        |
| Heavy reads, infrequent writes | Read-Through         |
| Ultra-low write latency needed | Write-Behind (Async) |
| Cache warming after restart    | Read-Through (lazy)  |
| Highly consistent cache needed | Write-Through        |
