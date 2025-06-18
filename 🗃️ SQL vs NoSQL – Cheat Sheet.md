# 🗃️ SQL vs NoSQL – Cheat Sheet

A quick comparison of **Relational (SQL)** vs **Non-Relational (NoSQL)** databases and when to use each.

---

## 🧩 Basic Differences

| Feature            | SQL (Relational DB)              | NoSQL (Non-Relational DB)               |
| ------------------ | -------------------------------- | --------------------------------------- |
| **Data Model**     | Tables with fixed schemas        | Flexible (JSON, key-value, graph, etc.) |
| **Schema**         | Rigid (predefined)               | Dynamic / schema-less                   |
| **Scaling**        | Vertical (scale-up)              | Horizontal (scale-out)                  |
| **Transactions**   | ACID-compliant                   | BASE (eventual consistency)             |
| **Query Language** | SQL (Structured Query Language)  | Varies (MongoQL, CQL, etc.)             |
| **Examples**       | MySQL, PostgreSQL, Oracle, MSSQL | MongoDB, Cassandra, DynamoDB, Redis     |

---

## 🧠 When to Choose SQL

✅ Choose **SQL (Relational DB)** when:

* [ ] You need **strong consistency** (e.g., bank transactions).
* [ ] Data is **highly structured** and **relational** (joins, constraints).
* [ ] You rely heavily on **complex queries** and **aggregations**.
* [ ] ACID properties are essential (Atomicity, Consistency, Isolation, Durability).
* [ ] You want **mature tooling and reporting support** (BI tools, JDBC, etc.).
* [ ] The application has a **fixed schema** and well-known requirements.

**Use Cases:**

* Banking and finance
* ERP/CRM systems
* E-commerce order systems
* Data warehousing (PostgreSQL with extensions, etc.)

---

## 🔄 When to Choose NoSQL

✅ Choose **NoSQL** when:

* [ ] You need **high write/read throughput** and horizontal scalability.
* [ ] Schema is **dynamic or evolving** over time.
* [ ] You store **large volumes of semi-structured/unstructured data** (e.g., JSON, logs).
* [ ] You are building **real-time apps** (chats, feeds, sensors).
* [ ] You need to store **nested documents**, **key-value pairs**, or **graphs**.

**Use Cases:**

* User profiles & session storage (Redis)
* Real-time analytics (Cassandra, DynamoDB)
* Content management & blogs (MongoDB)
* IoT, logging, and telemetry (InfluxDB, Elasticsearch)
* Social networks (Neo4j, ArangoDB)

---

## 🗃️ Data Types by NoSQL Type

| Type          | Description              | Examples           |
| ------------- | ------------------------ | ------------------ |
| **Document**  | Stores JSON/BSON objects | MongoDB, Couchbase |
| **Key-Value** | Simple key-value pairs   | Redis, DynamoDB    |
| **Columnar**  | Column-family storage    | Cassandra, HBase   |
| **Graph**     | Nodes and edges          | Neo4j, JanusGraph  |

---

## ⚖️ Trade-offs Summary

| Criteria        | SQL                          | NoSQL                        |
| --------------- | ---------------------------- | ---------------------------- |
| **Consistency** | Strong (ACID)                | Eventual (BASE)              |
| **Scalability** | Vertical                     | Horizontal                   |
| **Flexibility** | Rigid schema                 | Schema-less                  |
| **Performance** | Slower on large unstructured | Faster for distributed loads |
| **Joins**       | Strong support               | Limited or none              |

---

## 📌 Rule of Thumb

| Requirement                       | Pick This              |
| --------------------------------- | ---------------------- |
| Structured, relational data       | ✅ SQL                  |
| Evolving schema or document store | ✅ NoSQL (Document)     |
| High throughput, low latency      | ✅ NoSQL (KV, Columnar) |
| Graph-like relationships          | ✅ NoSQL (Graph DB)     |
| Strong transactional integrity    | ✅ SQL                  |
