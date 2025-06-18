# 🧩 Microservices Components Cheat Sheet

A quick reference to common components, patterns, and tools in a microservices architecture.

---

## 🏗️ Core Components

| Component                | Description                                                                                 |
| ------------------------ | ------------------------------------------------------------------------------------------- |
| **API Gateway**          | Entry point for clients. Handles routing, rate limiting, auth, etc.                         |
| **Service Registry**     | Tracks available service instances (e.g., Eureka, Consul, etcd).                            |
| **Config Server**        | Centralized externalized configuration for services (e.g., Spring Cloud Config, Consul KV). |
| **Service Discovery**    | Enables dynamic location of services (e.g., client-side or server-side discovery).          |
| **Microservices**        | Independent, loosely coupled services focusing on business capabilities.                    |
| **Database per Service** | Each service manages its own schema (polyglot persistence allowed).                         |

---

## 🔒 Security

| Feature               | Description                                   |
| --------------------- | --------------------------------------------- |
| **JWT/OAuth2**        | Token-based authentication (stateless).       |
| **API Key / HMAC**    | For internal service communication.           |
| **mTLS**              | Mutual TLS for service-to-service encryption. |
| **Role-based Access** | Fine-grained access control.                  |
| **Secret Management** | Use Vault, AWS Secrets Manager, etc.          |

---

## 🔀 Communication

| Type                  | Tools/Tech                              |
| --------------------- | --------------------------------------- |
| **Sync (HTTP/gRPC)**  | REST (JSON), gRPC (Protobuf)            |
| **Async (Messaging)** | Kafka, RabbitMQ, NATS, ActiveMQ         |
| **Event-Driven**      | Kafka/EventBridge + Event Sourcing/CQRS |
| **Circuit Breaker**   | Resilience4j, Hystrix (deprecated)      |
| **Retry/Fallback**    | Resilience4j Retry module, Spring Retry |

---

## 📦 Data Management Patterns

| Pattern                  | Description                                                           |
| ------------------------ | --------------------------------------------------------------------- |
| **Database per service** | Avoid shared schemas.                                                 |
| **Saga Pattern**         | Distributed transaction coordination (choreography vs orchestration). |
| **CQRS**                 | Separate read/write models for performance/scaling.                   |
| **Event Sourcing**       | Store state changes as a sequence of events.                          |

---

## 🚦 Observability

| Feature           | Tools                            |
| ----------------- | -------------------------------- |
| **Logging**       | ELK Stack, Loki, Fluentd         |
| **Tracing**       | Zipkin, Jaeger, OpenTelemetry    |
| **Metrics**       | Prometheus + Grafana             |
| **Health Checks** | Spring Boot Actuator, K8s probes |

---

## ☁️ Deployment

| Feature              | Tools                                      |
| -------------------- | ------------------------------------------ |
| **Containerization** | Docker                                     |
| **Orchestration**    | Kubernetes, ECS, Nomad                     |
| **Service Mesh**     | Istio, Linkerd, Consul Connect             |
| **CI/CD**            | GitHub Actions, GitLab CI, Jenkins, ArgoCD |

---

## ⚙️ Dev Practices

| Feature              | Description                                              |
| -------------------- | -------------------------------------------------------- |
| **12-Factor App**    | Set of principles for building modern cloud-native apps. |
| **Contract Testing** | Use tools like Pact to validate APIs between services.   |
| **Feature Flags**    | Toggle features at runtime (LaunchDarkly, Unleash).      |
| **Versioning**       | API versioning (URI/header-based).                       |
| **Rate Limiting**    | Limit excessive API usage (via Gateway or custom).       |

---

## 🔁 Resilience Patterns

| Pattern             | Use Case                                           |
| ------------------- | -------------------------------------------------- |
| **Retry**           | Handle transient failures.                         |
| **Circuit Breaker** | Prevent cascading failures.                        |
| **Bulkhead**        | Isolate failures across service threads/resources. |
| **Timeouts**        | Avoid waiting too long for slow dependencies.      |

---

## 📚 Example Stack (Spring Boot)

| Concern           | Tool                 |
| ----------------- | -------------------- |
| Config Management | Spring Cloud Config  |
| Service Discovery | Eureka               |
| API Gateway       | Spring Cloud Gateway |
| Circuit Breaker   | Resilience4j         |
| Messaging         | Kafka                |
| Tracing           | Sleuth + Zipkin      |
| Auth              | OAuth2 + JWT         |

