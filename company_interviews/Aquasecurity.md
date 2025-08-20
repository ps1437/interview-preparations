
# Interview Questions

1. How do you make sure Kafka events are published **only once**?
2. Why can one Kafka message be consumed by **two consumers** if they belong to **different consumer groups**?
3. If a client sends a request but then disconnects before processing finishes, how do you ensure the request is **rolled back**?
4. Retry Mechanism in Kafka
4. What are **ACID properties** in a database?
5. What are different types of **Transaction Propagation** in Spring?
6. What are different types of **Database Locks**?
7. Isolation level and use case
8. How deployment is working
9. How u make sure Zero Downtime during Deployment
10. AWS services and what you used in Realtime
11. Project Architecture
12. You have an integer array and 3 threads. Each thread should increment an element in the array, but **no two threads should update the same index at the same time**.How would you implement this in Java to ensure **index-level atomicity**?
13. Resiliency design pattern

---
## Answers
# Kafka Exactly-Once Event Publishing – Q&A

## 1. By default, what guarantee does Kafka provide?
- **Answer:** Kafka provides **at-least-once** delivery by default.  
  This means events may be delivered more than once in case of retries or failures.

---

## 2. How can we ensure that a Kafka producer publishes each message only once?
- **Answer:** Enable **idempotent producer** by setting:
  ```properties
  enable.idempotence=true

## 3. What is the role of `transactional.id` in Kafka producer?

* **Answer:** Setting a unique `transactional.id` allows the producer to use **transactions**:

  ```properties
  enable.idempotence=true
  transactional.id=my-transactional-producer
  ```

  * Ensures that either **all messages in the transaction are published once** or **none at all**.
  * Guarantees **exactly-once** semantics across topics and partitions.

---

## 4. Can consumers still see duplicates even if the producer uses idempotence?

* **Answer:** Yes.
  Consumers may see duplicates due to retries, partition rebalances, or offsets being replayed.
  Hence, **consumer applications must also be idempotent**.

---

## 5. How can we make consumers idempotent?

* **Answer:**

  * Track processed messages using `(topic, partition, offset)` or a **business key**.
  * Store processed IDs in a **DB/Cache (Redis, SQL with unique constraints)**.
  * Skip processing if the message was already handled.

---

## 6. What if I need to write to both a database and Kafka with exactly-once guarantees?

* **Answer:** Use the **Outbox Pattern**:

  * Write events into a dedicated **outbox table** within the same DB transaction.
  * A separate service or Debezium CDC publishes the events from the outbox to Kafka.
  * Ensures DB and Kafka remain consistent.


Good catch 👍 — this is one of those **Kafka consumer group** behaviors that often confuses people. Let’s break it down.

---

## ❓ Why would **one message be consumed by 2 consumers** in the same group?

### 1. **Rebalance in progress**

* If a consumer **crashes, joins, or leaves** a group → Kafka triggers a **rebalance**.
* During this rebalance, some partitions may temporarily get reassigned.
* If offsets are not committed properly, the new consumer may **re-read messages** that the previous one already read → looks like the same message was consumed twice.

---

### 2. **Offset not committed (or committed late)**

* Kafka tracks consumer progress via **offsets**.
* If your consumer processes a message but **fails to commit the offset**:

  * On restart, Kafka assumes the message was **not processed**.
  * It delivers the message again → **duplicate consumption**.

Example config to control commits:

```properties
enable.auto.commit=false
```

Then commit manually after processing:

```java
consumer.commitSync();
```

---

### 3. **At-least-once semantics**

* Kafka by design provides **at-least-once delivery** unless you combine:

  * Transactions (`transactional.id`)
  * Exactly-once processing logic
* Otherwise, duplicates are possible.

---

### 4. **Multiple partitions, fewer consumers**

* If a topic has fewer partitions than consumers in the group →
  some consumers remain **idle**.
* But if consumers are not in the **same group** (different group IDs), then **all groups receive all messages**.
  (This is probably not your case since you said “similar group,” but worth checking.)

---

## ✅ How to Prevent This

* Use **idempotent consumers** (deduplicate with keys or offsets).
* Ensure **offsets are committed only after successful processing**.
* Use **transactions** (`consume-transform-produce`) to combine reading and publishing into an atomic unit.
* Monitor **rebalance events** (e.g., implement `ConsumerRebalanceListener`).

---

👉 Quick question for you:
When you say *“2 consumers below to similar group”*, do you mean:

1. **Same group ID** (should share partitions, no duplicates expected except on rebalances/offset issues), or
2. **Different group IDs but similar names** (in which case duplicates are expected because each group gets the full copy of data)?

---
### Scenario Base REST
So the situation is:

* A client sends a REST API request.
* While your server is processing it (maybe writing to DB or calling downstream services), the **client disconnects / socket closes**.
* You want to **rollback the transaction** (DB changes, messages, etc.) if the client is no longer available.

---

## 🔑 Key Point

HTTP itself is **stateless** — once the request is sent, the server **doesn’t know immediately** if the client disconnects.
But many frameworks let you **detect connection loss** so you can rollback.

---

## ✅ Approaches

### 1. **Transaction Boundaries at Request Level**

* Start your DB transaction when request starts.
* Commit only when your processing is complete.
* If the client disconnects (before completion), the framework should cancel the request → transaction rolls back automatically if you haven’t committed.

👉 In **Spring Boot (with @Transactional)**:

```java
@PostMapping("/process")
@Transactional
public ResponseEntity<String> process(HttpServletRequest request) {
    if (request.isAsyncStarted() && !request.isAsyncSupported()) {
        throw new RuntimeException("Client disconnected");
    }
    // do DB operations
    return ResponseEntity.ok("Processed");
}
```

* If an exception is thrown → transaction rollback happens.

---

### 2. **Connection Listener**

In **Servlet containers (Tomcat/Jetty)**, you can detect socket close:

```java
request.getAsyncContext().addListener(new AsyncListener() {
    @Override
    public void onError(AsyncEvent event) {
        // rollback transaction
    }

    @Override
    public void onTimeout(AsyncEvent event) {
        // rollback
    }

    @Override
    public void onComplete(AsyncEvent event) {
        // commit if needed
    }

    @Override
    public void onStartAsync(AsyncEvent event) { }
});
```

---

### 3. **Deferred Commit**

Instead of committing to DB *during request*, you can:

* Write changes to a **staging table / queue**.
* Only **finalize commit** if the client confirms receipt (e.g., via ACK, polling pattern).
* If the client drops, the staged changes are cleaned up.
  👉 This is similar to the **Outbox pattern**.

---

### 4. **Spring Transaction Rollback Example**

```java
@PostMapping("/order")
public ResponseEntity<String> createOrder(HttpServletRequest request) {
    TransactionStatus status = txManager.getTransaction(new DefaultTransactionDefinition());
    try {
        // insert order
        jdbcTemplate.update("INSERT INTO orders ...");

        if (!request.isAsyncStarted() && request.isAsyncSupported()) {
            // client disconnected
            txManager.rollback(status);
            return ResponseEntity.status(HttpStatus.SERVICE_UNAVAILABLE).body("Client disconnected");
        }

        txManager.commit(status);
        return ResponseEntity.ok("Success");
    } catch (Exception e) {
        txManager.rollback(status);
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body("Error");
    }
}
```

---

## ⚠️ Important

* If the **client disconnects after you commit**, rollback is **not possible** (DB has already persisted).
* Best practice: **process fully on server, then commit once done**, regardless of client status.
  (Client can always retry safely if idempotent APIs are designed.)
---
# Database Transactions – Q&A

## 1. What are the ACID properties in a transaction?
- **Atomicity:**  
  Ensures that all operations in a transaction are executed successfully or none at all.  
  (All-or-nothing principle.)

- **Consistency:**  
  A transaction moves the database from one valid state to another valid state.  
  (Integrity constraints must hold.)

- **Isolation:**  
  Transactions are executed as if they are the only transaction in the system, even if many run concurrently.

- **Durability:**  
  Once a transaction is committed, its changes are permanent, even in case of a system crash.

---

## 2. What are the different Isolation Levels in databases?
Isolation levels define how transaction integrity is visible to other transactions.

1. **Read Uncommitted**  
   - Allows dirty reads, non-repeatable reads, and phantom reads.  
   - Lowest isolation, highest concurrency.

2. **Read Committed**  
   - Prevents dirty reads.  
   - Non-repeatable reads and phantom reads may still occur.  
   - Default in most databases (e.g., Oracle, SQL Server).

3. **Repeatable Read**  
   - Prevents dirty reads and non-repeatable reads.  
   - Phantom reads may still occur.  
   - Default in MySQL InnoDB.

4. **Serializable**  
   - Prevents dirty reads, non-repeatable reads, and phantom reads.  
   - Highest isolation, lowest concurrency.

---

## 3. What are Propagation types in Spring transactions?
Propagation defines how a method participates in an existing transaction.

- **REQUIRED (default):**  
  Join the existing transaction, or create a new one if none exists.

- **REQUIRES_NEW:**  
  Always start a new transaction, suspending the existing one.

- **MANDATORY:**  
  Must run inside an existing transaction; otherwise, throw an exception.

- **SUPPORTS:**  
  If a transaction exists, join it; otherwise, run without one.

- **NOT_SUPPORTED:**  
  Suspend the current transaction and run without one.

- **NEVER:**  
  Must run without a transaction; throw an exception if one exists.

- **NESTED:**  
  Run within a nested transaction if one exists, otherwise create a new one.  
  Rollback affects only the nested transaction, not the parent.

---

## 4. What types of locks are used in databases?
Locks prevent conflicts when multiple transactions access the same resource.

### a) **By Granularity**
- **Row-level Lock:** Locks a specific row. High concurrency.  
- **Table-level Lock:** Locks the entire table. Simple but lower concurrency.  
- **Page-level Lock:** Locks a data page (set of rows). Middle ground.

### b) **By Type**
- **Shared Lock (S):**  
  Multiple transactions can read the same resource, but not write.  

- **Exclusive Lock (X):**  
  Only one transaction can read/write. Others are blocked.  

- **Update Lock (U):**  
  Used when a transaction intends to update but avoids deadlock between readers and updaters.  

- **Intent Locks (IS, IX):**  
  Show an intention to acquire locks at a finer granularity (row-level inside a table-level lock).

### c) **By Duration**
- **Short-term / Transactional Lock:** Released at the end of the transaction.  
- **Long-term Lock:** Explicitly acquired and released later.  

---

## 5. Summary Table

| Concept        | Key Point |
|----------------|-----------|
| **Atomicity**  | All-or-nothing execution |
| **Consistency**| Valid state transitions |
| **Isolation**  | Transactions act independently |
| **Durability** | Changes survive failures |
| **Isolation Levels** | Read Uncommitted → Serializable |
| **Propagation** | REQUIRED, REQUIRES_NEW, SUPPORTS, etc. |
| **Locks** | Shared, Exclusive, Update, Intent, Row/Table/Page |


# ❓ Question  
You have an integer array and 3 threads. Each thread should increment an element in the array, but **no two threads should update the same index at the same time**.  

How would you implement this in Java to ensure **index-level atomicity**?

---

# ✅ Answer  

There are multiple approaches to ensure index-level atomic updates in Java:

---

## 🔹 1. Using `AtomicInteger` as Task Distributor  
We can use an `AtomicInteger` counter to assign **unique indexes** to threads.  

```java
import java.util.concurrent.atomic.AtomicInteger;

public class AtomicIntegerIncrementExample {
    private final int[] array = {1, 2, 3, 4, 5, 6, 7, 8};
    private final AtomicInteger index = new AtomicInteger(0);

    public void incrementByOne() {
        int i = index.getAndIncrement(); 
        if (i < array.length) {
            array[i] = array[i] + 1;
        }
    }
}
````

👉 Ensures **no two threads get the same index**.
👉 Simple and lock-free.

---

## 🔹 2. Using `AtomicIntegerArray`

If we want multiple threads to safely update different (or even the same) indexes concurrently, we can use `AtomicIntegerArray`.

```java
import java.util.concurrent.atomic.AtomicIntegerArray;

public class AtomicIntegerArrayExample {
    private final AtomicIntegerArray array = new AtomicIntegerArray(new int[]{1,2,3,4,5,6,7,8});

    public void incrementByOne(int index) {
        array.incrementAndGet(index); // thread-safe per index
    }

    public int get(int index) {
        return array.get(index);
    }
}
```

👉 Provides **atomic per-index updates** without explicit locking.
👉 Best when multiple threads may hit the same index.

---

## 🔹 3. Using Per-Index Locks (`ReentrantLock[]`)

For more complex updates (not just `+1`), you can use an array of locks.

```java
import java.util.concurrent.locks.ReentrantLock;

public class IndexLockArray {
    private final int[] array = {1,2,3,4,5,6,7,8};
    private final ReentrantLock[] locks;

    public IndexLockArray(int size) {
        locks = new ReentrantLock[size];
        for (int i = 0; i < size; i++) {
            locks[i] = new ReentrantLock();
        }
    }

    public void incrementByOne(int index) {
        locks[index].lock();
        try {
            array[index]++;
        } finally {
            locks[index].unlock();
        }
    }

    public int get(int index) {
        return array[index];
    }
}
```

👉 Allows **parallel updates to different indexes**.
👉 Only locks the index being updated.

---

## 🔹 4. Using `synchronized` on Per-Index Objects

Instead of locks, use an `Object[]` as index-level monitors.

```java
public class IndexSynchronizedArray {
    private final int[] array = {1,2,3,4,5,6,7,8};
    private final Object[] locks;

    public IndexSynchronizedArray(int size) {
        locks = new Object[size];
        for (int i = 0; i < size; i++) {
            locks[i] = new Object();
        }
    }

    public void incrementByOne(int index) {
        synchronized (locks[index]) {
            array[index]++;
        }
    }

    public int get(int index) {
        return array[index];
    }
}
```

👉 Simpler than `ReentrantLock[]`, but less flexible.

---

# 📝 Key Takeaways

1. **Simple index distribution** → Use `AtomicInteger` counter.
2. **Atomic per-index increments** → Use `AtomicIntegerArray`.
3. **Complex updates per index** → Use `ReentrantLock[]` or `synchronized` per index.
4. Avoid a **global lock** on the whole array (kills concurrency).


