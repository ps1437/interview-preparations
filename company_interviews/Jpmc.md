# 🔧 Project Overview & High-Level Design (HLD)

## 📦 Component View
- **API Gateway** – Entry point for client requests
- **Service Layer** – Microservices built using Spring Boot
- **Database Layer** – SQL/NoSQL based on use case
- **Message Broker** – Apache Kafka for async communication
- **Cache Layer** – Redis for low-latency reads
- **Security Layer** – JWT-based Auth (Spring Security)
- **CI/CD** – GitHub Actions + Docker + Kubernetes

---

# 🧠 SQL vs NoSQL

## Why NoSQL?
- Schema flexibility
- Horizontal scalability
- High throughput (e.g., Kafka message logs, IoT)

## When to use SQL?
- Relational data
- Transactions (ACID compliance)
- Strong consistency required

## 📚 Types of NoSQL Databases
| Type        | Examples             | Use Case                          |
|-------------|----------------------|-----------------------------------|
| Document    | MongoDB, Couchbase   | User profiles, content mgmt       |
| Key-Value   | Redis, DynamoDB      | Session store, caching            |
| Column-Family | Cassandra, HBase   | Time-series data, logging         |
| Graph       | Neo4j, JanusGraph    | Social networks, recommendation   |

---

# 📌 Java Topics

## ✅ Immutability of a Class
- Make class `final`
- All fields `private final`
- No setters, only getters
- Deep copy of mutable objects in constructor/getters

## ✅ Runnable vs Callable
- `Runnable` – No return value, can't throw checked exceptions
- `Callable` – Returns value (`Future`), can throw checked exceptions

## ✅ Java 17 Features Used
- Sealed Classes
- Records for DTOs
- Pattern Matching for `instanceof`
- Enhanced switch expression

---

# 💡 Design Patterns in Real Projects

| Pattern            | Use Case                                |
|--------------------|------------------------------------------|
| Singleton          | Logger, Configuration                   |
| Factory            | Object creation (e.g., Notification)     |
| Strategy           | Payment method switching                 |
| Circuit Breaker    | Microservice fault tolerance (Resilience4j) |
| Builder            | Complex object construction (DTOs)       |

---

# 🔐 Spring Security + JWT

- Authentication via JWT token
- Authorization based on user roles/claims
- `OncePerRequestFilter` for validating token
- Claims: userId, role, expiry, permissions

---
# 🪵 Logging in Distributed Systems

## 🐳 How to Ensure Logs Are Not Lost in Docker Containers

In distributed systems (e.g., microservices running in Docker), logs can get lost when:
- A container crashes or restarts
- Logs are written only to local files inside the container

### ✅ Best Practices to Avoid Log Loss
1. **Log to STDOUT/STDERR**
   - Use console appenders (e.g., in Logback/Log4j)
   - Docker captures these and can be redirected to centralized systems

2. **Use a Log Aggregator**
   - Forward container logs to tools like:
     - **ELK Stack** (Elasticsearch, Logstash, Kibana)
     - **EFK Stack** (Elasticsearch, Fluentd, Kibana)
     - **Grafana Loki**
     - **Datadog**, **New Relic**, **Splunk**

3. **Persistent Volume for Logs (Not Recommended)**
   - Bind-mount host volumes to store logs outside the container
   - Not scalable and prone to disk fill-up

4. **Log Rotation**
   - Avoid unbounded log file growth inside containers
   - Use `logrotate` or Docker's log driver options

5. **Docker Logging Drivers**
   - Set logging driver: `json-file`, `syslog`, `fluentd`, `gelf`, etc.
   - Example:
     ```bash
     docker run --log-driver=fluentd my-app
     ```

## 🤖 Logging Agents Use Case

Logging agents are lightweight background daemons or sidecars that collect, transform, and forward logs.



---

# 🪙 Kafka Concepts

## ✅ Consumer Group
- Each consumer in a group reads from different partitions
- Ensures horizontal scaling

## ✅ Idempotency
- Achieved using unique message key or ID in DB
- Prevent duplicate processing

## ✅ Offset Reset Mechanism
- `earliest` – from beginning
- `latest` – from end
- `none` – fail if no offset is found

---

# 🛠️ Debugging & RCA (Root Cause Analysis)

- Use logs (ELK), stack traces
- Thread dumps, heap dumps for memory issues
- Profilers: JFR, YourKit, VisualVM
- APM Tools: New Relic, Datadog

---

# 🚨 Application Performance Bottleneck

- Use APM for method-level tracing
- Check GC logs, DB slow queries, thread starvation
- Analyze latency at service/API/DB/cache levels

---

# 🧠 Rate Limiter

## Use Case
- API abuse protection
- Prevent DDoS
- Fair usage policy

## Scenario
- Limit 10 requests per IP per minute (using Bucket4j or Redis)

---

# 🧪 Testing Concepts

## ✅ SPY vs MOCK vs STUB
| Type   | Behavior |
|--------|----------|
| Stub   | Returns predefined response |
| Mock   | Verifies behavior (interaction) |
| Spy    | Real object but can override methods |

## ✅ TestContainers
- Spin up real DB/Kafka for integration tests
- Docker-based testing environments

## ✅ Kafka Testing
- Use `EmbeddedKafka` or TestContainers
- Validate producer output & consumer consumption
- Manual offset assertions for idempotency

---

# 🚀 Deployment & CI/CD

## ✅ How Deployment Happens
- Code push triggers GitHub Actions
- Build → Test → Dockerize → Push to DockerHub/ECR
- Deploy to Kubernetes/EC2/Elastic Beanstalk

## ✅ GitHub Actions Use Case
- CI: Lint, Unit Test
- CD: Docker image build and deploy
- Infra: Terraform/Helm integration

## ✅ Contract Testing
- Tool: Pact or Spring Cloud Contract
- Ensures provider and consumer are in sync
- Reduces integration failures in prod

## ✅ Build Pipeline
1. Lint → Unit Test → Build
2. Integration Test → Docker Build
3. Image Push to Registry
4. Deploy to Kubernetes/AWS
5. Run E2E tests (Postman, Cypress)

## ✅ Deployment & Testing Process
- Canary releases or Blue-Green
- Automated tests in CI
- Smoke test post-deploy

---

# 📊 SQL Query: 4th Highest Salary

```sql
SELECT DISTINCT salary
FROM employee
ORDER BY salary DESC
LIMIT 1 OFFSET 3;
