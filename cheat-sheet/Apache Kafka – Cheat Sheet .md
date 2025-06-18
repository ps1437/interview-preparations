# 🔄 Apache Kafka – Cheat Sheet

A quick reference for Kafka producers, consumers, delivery guarantees, ordering, and partitioning strategies.

---

## 🔌 Kafka Producer Properties

| Property                  | Description                                             |
| ------------------------- | ------------------------------------------------------- |
| `acks=0`                  | Fire-and-forget (no guarantee)                          |
| `acks=1`                  | Leader-only ack (fast, less reliable)                   |
| `acks=all` or `-1`        | All replicas ack (strong durability)                    |
| `retries`                 | Number of retry attempts on failure                     |
| `enable.idempotence=true` | Ensures no duplicate writes (required for exactly-once) |
| `linger.ms`               | Wait time to batch more messages                        |
| `batch.size`              | Size of the message batch                               |

---

## 🛠 Idempotence in Kafka

| Feature                   | Description                                                            |
| ------------------------- | ---------------------------------------------------------------------- |
| `enable.idempotence=true` | Guarantees **no duplicates** on retries                                |
| Internally uses           | **Producer ID + Sequence Number**                                      |
| Required for              | **Exactly-once** and **EOS**                                           |
| Works with                | Kafka 0.11+                                                            |
| Requires                  | `acks=all`, `retries > 0`, `max.in.flight.requests.per.connection ≤ 5` |

✅ Ensures **safety**: same message will **not be written twice**, even during retries.

---

## 📦 Delivery Guarantees

| Guarantee         | Description                          | Kafka Setting              |
| ----------------- | ------------------------------------ | -------------------------- |
| **At most once**  | May lose data, but never duplicates  | No retries, acks=0         |
| **At least once** | May deliver duplicates, no data loss | retries > 0, acks=all      |
| **Exactly once**  | No loss, no duplicates               | Idempotence + Transactions |

---

## 🔁 Ordering

| Topic             | Ordering Guarantee                                            | How to Ensure |
| ----------------- | ------------------------------------------------------------- | ------------- |
| **Per topic**     | ❌ No ordering across partitions                               |               |
| **Per partition** | ✅ Strong ordering within a partition                          |               |
| Use case          | Use the same **key** for messages that need to maintain order |               |

---

## ⛓ Kafka Consumer Properties

| Property             | Description                                             |
| -------------------- | ------------------------------------------------------- |
| `group.id`           | Logical consumer group identifier                       |
| `auto.offset.reset`  | `latest` (default) or `earliest`                        |
| `enable.auto.commit` | Whether consumer automatically commits offsets          |
| `isolation.level`    | `read_committed` for EOS, `read_uncommitted` by default |

---

## 🧪 Kafka Transaction (Exactly-Once Semantics - EOS)

| Concept                      | Description                                        |
| ---------------------------- | -------------------------------------------------- |
| `transactional.id`           | Unique ID for the producer to track transactions   |
| `initTransactions()`         | Start transaction                                  |
| `beginTransaction()`         | Open a new transaction                             |
| `sendOffsetsToTransaction()` | Commit offsets as part of txn (used with consumer) |
| `commitTransaction()`        | Commit atomically                                  |
| `abortTransaction()`         | Roll back                                          |

Use when **producing + consuming + writing to DB** in a single atomic flow.

---

## 🧮 Symmetric Partitioning

| Concept                    | Description                                             |
| -------------------------- | ------------------------------------------------------- |
| **Symmetric partitioning** | Ensures same key is always routed to same partition     |
| Achieved by                | Providing same **key** + consistent **partitioner**     |
| Use case                   | Maintain **ordering** and **data locality**             |
| Custom partitioner         | Override `DefaultPartitioner` to implement custom logic |

---

## 📌 Kafka Patterns & Tips

| Topic                         | Best Practice                                      |
| ----------------------------- | -------------------------------------------------- |
| High throughput writes        | Use `linger.ms`, `batch.size`, compression         |
| Message duplication           | Enable **idempotence**                             |
| Consistency in microservices  | Use **Kafka transactions** for atomic ops          |
| Scale consumer groups         | Add more consumers to increase parallelism         |
| Low latency + order guarantee | One partition per key, use fewer inflight requests |

---

## 🧠 Common Use Cases

| Use Case          | Strategy                                       |
| ----------------- | ---------------------------------------------- |
| Audit logs        | Partition by timestamp or user ID              |
| Order management  | Partition by order ID (maintain ordering)      |
| Metrics ingestion | Keyless (random partitioning) for parallelism  |
| Payment systems   | Idempotence + Transactions + Ordering required |

---

## 🛑 Warning Zones

| Scenario                     | Issue                                    |
| ---------------------------- | ---------------------------------------- |
| Multiple partitions, no keys | No ordering guarantee                    |
| Too many inflight requests   | Can break idempotency (set ≤ 5)          |
| Unbounded retries            | Can overload system, add backoff         |
| Large messages               | May need to increase `max.message.bytes` |
