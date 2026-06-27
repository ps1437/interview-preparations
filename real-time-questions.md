# Java Microservices Interview Questions

## 1. Optimistic vs Pessimistic Locking

1. What is optimistic locking?
2. What is pessimistic locking?
3. What is the difference between optimistic locking and pessimistic locking?
4. When should we use optimistic locking?
5. When should we use pessimistic locking?
6. How does `@Version` work in JPA?
7. What happens when two users update the same row using optimistic locking?
8. How do you handle `OptimisticLockException`?
9. What is row-level locking?
10. What is `SELECT FOR UPDATE`?
11. What are real-time use cases of optimistic locking?
12. What are real-time use cases of pessimistic locking?
13. Which locking approach is better for order status update?
14. Which locking approach is better for wallet debit?
15. Which locking approach is better for seat booking?
16. How do you prevent lost updates in a database?
17. How do you handle two users updating the same order?
18. How do you handle two transactions modifying the same inventory record?
19. How do you make sure only one request updates order status at a time?
20. How do you prevent an order from being cancelled after shipment?

---

## 2. Concurrent Operations and Database Real-time Use Cases

1. What is a concurrent operation?
2. What is a race condition?
3. What is a lost update problem?
4. What is dirty read?
5. What is non-repeatable read?
6. What is phantom read?
7. What are ACID properties?
8. What are transaction isolation levels?
9. Which isolation level prevents dirty reads?
10. Which isolation level prevents phantom reads?
11. How do you handle concurrent inventory deduction?
12. How do you handle concurrent wallet debit?
13. How do you handle concurrent ticket booking?
14. How do you handle concurrent order cancellation and shipment?
15. How do you execute two transactions concurrently when both modify the same order?
16. How do you prevent duplicate payment processing?
17. How do you prevent duplicate order creation?
18. How do you handle duplicate API requests?
19. How do you design idempotent APIs?
20. How do you handle deadlocks in a database?
21. How do you identify deadlock issues?
22. How do you avoid deadlocks in transactions?
23. How do you handle transaction rollback in Spring Boot?
24. What is transaction propagation?
25. Difference between `REQUIRED` and `REQUIRES_NEW`.
26. How do you handle partial failure in a transaction?
27. What is atomic update query?
28. How can Kafka help in concurrent processing?
29. How do you process all events of the same order sequentially?
30. How do you design a safe order update flow?

---

## 3. Kafka vs RabbitMQ

1. What is Kafka?
2. What is RabbitMQ?
3. What is the difference between Kafka and RabbitMQ?
4. When should we use Kafka?
5. When should we use RabbitMQ?
6. Kafka is pull-based or push-based?
7. RabbitMQ is pull-based or push-based?
8. How does Kafka store messages?
9. How does RabbitMQ handle consumed messages?
10. What is a Kafka topic?
11. What is a Kafka partition?
12. What is Kafka broker?
13. What is Kafka producer?
14. What is Kafka consumer?
15. What is Kafka consumer group?
16. What is Kafka offset?
17. What is consumer lag?
18. What is Kafka rebalance?
19. How does Kafka maintain ordering?
20. How do you process all events of one order in order?
21. How do you retry failed Kafka messages?
22. What is DLQ in Kafka?
23. What is a poison message?
24. How do you handle duplicate Kafka events?
25. What is an idempotent consumer?
26. What is an idempotent producer?
27. What is at-most-once delivery?
28. What is at-least-once delivery?
29. What is exactly-once processing?
30. What is schema registry?
31. How do you handle schema evolution?
32. How do you replay Kafka events?
33. What happens if Kafka consumer goes down?
34. How do you decide number of partitions?
35. What real-time use cases have you implemented using Kafka?
36. Can Kafka replace REST API?
37. Can Kafka replace database?
38. How do you monitor Kafka consumer lag?
39. How do you handle out-of-order Kafka events?
40. How do you handle large messages in Kafka?

---

## 4. Elasticsearch Use Cases

1. What is Elasticsearch?
2. Why do we use Elasticsearch?
3. Why not use only database search?
4. What are real-time use cases of Elasticsearch?
5. Who will give data to Elasticsearch?
6. Is Elasticsearch the source of truth?
7. What kind of data should be stored in Elasticsearch?
8. What kind of data should not be stored in Elasticsearch?
9. How do you keep database and Elasticsearch in sync?
10. How does Kafka help in Elasticsearch indexing?
11. What happens if Elasticsearch indexing fails?
12. How do you handle stale data in Elasticsearch?
13. How do you rebuild an Elasticsearch index?
14. What is zero-downtime reindexing?
15. What is Elasticsearch index?
16. What is Elasticsearch document?
17. What is Elasticsearch mapping?
18. What is analyzer in Elasticsearch?
19. Difference between `match` and `term` query.
20. How do you implement partial search?
21. How do you implement autocomplete?
22. How do you search by title or partial ID?
23. How do you handle millions of records in Elasticsearch?
24. How do you secure sensitive data in Elasticsearch?
25. How do you delete data from Elasticsearch when database data is deleted?
26. How do you handle duplicate documents in Elasticsearch?
27. What is Elasticsearch alias?
28. How do you optimize Elasticsearch search performance?
29. How do you paginate large search results?
30. Difference between offset pagination and search-after.
31. What is eventual consistency in Elasticsearch?
32. What is refresh interval in Elasticsearch?
33. What is inverted index?
34. What is full-text search?
35. What are filters in Elasticsearch?

---

## 5. Garbage Collector

1. What is garbage collection in Java?
2. Why is garbage collection required?
3. What are different types of garbage collectors in Java?
4. What is Serial GC?
5. What is Parallel GC?
6. What is G1 GC?
7. What is ZGC?
8. What is Shenandoah GC?
9. Which GC is default in modern Java?
10. Which GC is suitable for Spring Boot microservices?
11. Which GC is suitable for low-latency applications?
12. Which GC is suitable for batch processing?
13. What is young generation?
14. What is old generation?
15. What is minor GC?
16. What is major GC?
17. What is full GC?
18. What is stop-the-world pause?
19. How do you identify GC issues?
20. How do you tune GC in production?
21. How do you analyze GC logs?
22. What causes high GC activity?
23. What causes memory leak in Java?
24. How do you troubleshoot `OutOfMemoryError`?
25. What is heap dump?
26. What is thread dump?
27. Difference between heap memory and stack memory.
28. What is Metaspace?
29. What happens if Metaspace is full?
30. How do you monitor JVM memory in production?

---

## 6. Class Loader System

1. What is class loading in Java?
2. What is ClassLoader?
3. What are the types of class loaders in Java?
4. What is Bootstrap ClassLoader?
5. What is Platform ClassLoader?
6. What is Application ClassLoader?
7. What is Custom ClassLoader?
8. What is parent delegation model?
9. Why does Java use parent delegation?
10. Can we create our own ClassLoader?
11. What are real-time use cases of Custom ClassLoader?
12. How do application servers use ClassLoaders?
13. How does Spring Boot load classes?
14. What is classpath?
15. What is module path?
16. Difference between classpath and module path.
17. What is `ClassNotFoundException`?
18. What is `NoClassDefFoundError`?
19. Difference between `ClassNotFoundException` and `NoClassDefFoundError`.
20. How do you debug class loading issues?

---

## 7. Project Discussion Questions

1. Tell me about your current project.
2. What is the architecture of your project?
3. What are the main modules in your project?
4. What is your role in the project?
5. What technologies are used in your project?
6. Why did you choose microservices architecture?
7. How many microservices are there in your project?
8. How do your microservices communicate?
9. Which services communicate synchronously?
10. Which services communicate asynchronously?
11. Where have you used Kafka in your project?
12. Where have you used Elasticsearch in your project?
13. Where have you used Redis in your project?
14. How do you secure your APIs?
15. How do you handle authentication and authorization?
16. How do you handle service-to-service communication?
17. How do you handle failures in your project?
18. How do you monitor your microservices?
19. How do you trace requests across services?
20. How do you handle logging in your project?
21. How do you deploy your project?
22. Are you using Docker?
23. Are you using Kubernetes?
24. How do you handle configuration management?
25. How do you handle secrets?
26. How do you handle database migration?
27. How do you handle production issues?
28. What was the most complex feature you worked on?
29. What was your biggest technical contribution?
30. What would you improve in your current project?

---

## 8. Technical Difficulties Faced in Career

1. Tell me about one difficult technical issue you faced.
2. Tell me about one production issue you handled.
3. Tell me about one performance issue you solved.
4. Tell me about one database issue you solved.
5. Tell me about one Kafka issue you solved.
6. Tell me about one Elasticsearch issue you solved.
7. Tell me about one memory issue you solved.
8. Tell me about one GC issue you solved.
9. Tell me about one API latency issue you solved.
10. Tell me about one deployment issue you solved.
11. Tell me about one security issue you handled.
12. Tell me about one data consistency issue you solved.
13. Tell me about one microservice communication issue you solved.
14. Tell me about one issue caused by duplicate events.
15. Tell me about one issue caused by missing idempotency.
16. Tell me about one issue caused by wrong transaction handling.
17. Tell me about one issue caused by bad database indexing.
18. Tell me about one issue caused by poor logging.
19. Tell me about one issue caused by missing monitoring.
20. Tell me about one issue caused by technical debt.

---

## 9. Junior Mentoring Questions

1. Have you mentored junior developers?
2. What kind of technical help do you give to juniors?
3. How do you help juniors with API design?
4. How do you help juniors with debugging?
5. How do you help juniors with code quality?
6. How do you help juniors understand microservices?
7. How do you help juniors understand Kafka?
8. How do you help juniors understand database transactions?
9. How do you help juniors write unit tests?
10. How do you review junior developers' code?
11. Give one example where you helped a junior developer fix a production issue.
12. Give one example where you helped a junior developer improve API design.
13. Give one example where you helped a junior developer improve SQL query performance.
14. Give one example where you helped a junior developer understand exception handling.
15. Give one example where you helped a junior developer understand clean code.
16. How do you make juniors independent?
17. How do you explain technical concepts to juniors?
18. How do you handle repeated mistakes from juniors?
19. How do you give code review comments?
20. How do you balance delivery pressure and mentoring?

---

## 10. Rate Limiter

1. What is rate limiting?
2. Why do we need rate limiting?
3. What are real-time use cases of rate limiter?
4. How do you rate limit login API?
5. How do you rate limit OTP API?
6. How do you rate limit payment API?
7. How do you rate limit public APIs?
8. How do you rate limit search APIs?
9. What is fixed window algorithm?
10. What is sliding window algorithm?
11. What is token bucket algorithm?
12. What is leaky bucket algorithm?
13. Which rate limiting algorithm is better for APIs?
14. How do you implement rate limiter using Redis?
15. Where should rate limiter be implemented?
16. Can rate limiter be implemented at API Gateway?
17. How do you rate limit per user?
18. How do you rate limit per IP address?
19. How do you rate limit per customer plan?
20. What response code should be returned when rate limit is exceeded?
21. Difference between rate limiting and throttling.
22. How do you handle distributed rate limiting?
23. How do you prevent brute-force login attacks?
24. How do you prevent OTP abuse?
25. How do you monitor rate limiter behavior?

---

## 11. Saga Pattern

1. What is Saga pattern?
2. Why do we need Saga pattern?
3. How does Saga solve distributed transaction problems?
4. What is choreography-based Saga?
5. What is orchestration-based Saga?
6. Difference between choreography and orchestration Saga.
7. When should we use choreography Saga?
8. When should we use orchestration Saga?
9. What is compensating transaction?
10. What happens if payment fails in Saga?
11. How do you rollback inventory reservation in Saga?
12. How do you rollback order creation in Saga?
13. How do you handle partial failure in Saga?
14. How do you handle retry in Saga?
15. How do you handle duplicate events in Saga?
16. How do you maintain Saga state?
17. How do you track Saga execution?
18. How do you handle timeout in Saga?
19. What are the challenges of Saga pattern?
20. How do you test Saga flows?
21. How do you debug Saga failures?
22. What is eventual consistency in Saga?
23. How do you notify users during Saga failure?
24. How do you design order-payment-inventory Saga?
25. How do you design refund Saga?

---

## 12. Event-Driven Architecture

1. What is event-driven architecture?
2. Why do we use event-driven architecture?
3. What is an event?
4. What is event producer?
5. What is event consumer?
6. What is event broker?
7. What are the benefits of event-driven architecture?
8. What are the challenges of event-driven architecture?
9. Difference between event and command.
10. Difference between event-driven and request-response architecture.
11. Where have you used event-driven architecture?
12. How do you handle duplicate events?
13. How do you handle out-of-order events?
14. How do you handle event replay?
15. How do you handle schema changes in events?
16. How do you version events?
17. What is idempotency in event-driven systems?
18. How do you monitor event-driven systems?
19. How do you debug event-driven systems?
20. What is correlation ID?
21. What is causation ID?
22. What is eventual consistency?
23. How do you ensure event ordering?
24. How do you design event names?
25. What should be included in an event payload?
26. What should not be included in an event payload?
27. How do you secure event data?
28. How do you handle failed events?
29. What is DLQ?
30. How do you reprocess DLQ messages?

---

## 13. Resiliency in Microservices

1. What is resiliency in microservices?
2. Why is resiliency important?
3. What is timeout?
4. Why should every service call have timeout?
5. What is retry?
6. When should retry be used?
7. When should retry not be used?
8. What is retry storm?
9. What is circuit breaker?
10. What are circuit breaker states?
11. What is fallback?
12. What is bulkhead pattern?
13. What is rate limiting?
14. What is throttling?
15. What is backpressure?
16. What is graceful degradation?
17. What is fail-fast?
18. What is DLQ?
19. What is idempotency?
20. How do you handle downstream service failure?
21. How do you handle payment service downtime?
22. How do you handle inventory service downtime?
23. How do you handle notification service failure?
24. How do you handle partial outage?
25. How do you monitor service health?
26. What metrics do you track for resiliency?
27. How do you test resiliency?
28. What is chaos testing?
29. How do you prevent cascading failure?
30. How do you design a fault-tolerant microservice?

---

## 14. Payment Failure and Rollback in Microservices

1. How do you handle payment failure in microservices?
2. How does rollback happen in Saga?
3. Is Saga rollback same as database rollback?
4. What is compensating transaction?
5. What happens if payment fails after inventory is reserved?
6. What happens if payment succeeds but order update fails?
7. What happens if inventory reservation fails after order is created?
8. How do you handle refund flow?
9. How do you handle duplicate payment callbacks?
10. How do you handle payment gateway timeout?
11. How do you handle payment success event arriving late?
12. How do you handle payment failed event arriving after success?
13. How do you ensure payment API is idempotent?
14. How do you design order status transitions?
15. How do you prevent double debit?
16. How do you prevent duplicate refund?
17. How do you handle reconciliation with payment gateway?
18. How do you handle failed payment events?
19. How do you handle DLQ in payment flow?
20. How do you audit payment transactions?

---

## 15. Technical Debt, Issues, and Challenges

1. What is technical debt?
2. What technical debt have you handled?
3. Tell me about one technical debt issue in your project.
4. How did you fix tightly coupled services?
5. How did you fix slow APIs?
6. How did you fix poor exception handling?
7. How did you fix missing logging?
8. How did you fix missing monitoring?
9. How did you fix duplicate code?
10. How did you fix bad database design?
11. How did you fix bad API design?
12. How did you fix missing test coverage?
13. How did you fix deployment issues?
14. How did you fix configuration issues?
15. How did you fix security issues?
16. How do you prioritize technical debt?
17. How do you convince business to fix technical debt?
18. How do you handle technical debt during sprint delivery?
19. How do you avoid creating new technical debt?
20. What was the biggest technical challenge you faced?

---

## 16. Achievements

1. What are your key achievements?
2. What is your biggest technical achievement?
3. What performance improvement have you done?
4. What production issue did you prevent?
5. What automation did you introduce?
6. What process improvement did you bring?
7. How did you improve code quality?
8. How did you improve API performance?
9. How did you improve system reliability?
10. How did you improve deployment process?
11. How did you reduce production defects?
12. How did you help the team deliver faster?
13. How did you mentor team members?
14. How did you improve monitoring or observability?
15. How did you improve database performance?
16. How did you improve search performance?
17. How did you improve Kafka processing?
18. How did you improve microservice resiliency?
19. How did you contribute outside your assigned task?
20. What achievement are you most proud of?

---

## 17. Additional Real-time Java Microservices Questions

1. How do you design an order management system?
2. How do you design a payment system?
3. How do you design an inventory system?
4. How do you design a notification system?
5. How do you design a product search system?
6. How do you design an audit logging system?
7. How do you design a user activity tracking system?
8. How do you design a file upload service?
9. How do you design a report generation service?
10. How do you design a wallet system?
11. How do you design idempotency for payment APIs?
12. How do you design retry for failed orders?
13. How do you design DLQ reprocessing?
14. How do you design order status state machine?
15. How do you design API pagination?
16. How do you design API filtering and sorting?
17. How do you design API versioning?
18. How do you design global exception handling?
19. How do you design validation for REST APIs?
20. How do you design secure REST APIs?
21. How do you secure service-to-service communication?
22. How do you implement JWT authentication?
23. How do you implement role-based access control?
24. How do you implement audit logging?
25. How do you implement distributed tracing?
26. How do you implement correlation ID?
27. How do you implement centralized logging?
28. How do you implement health checks?
29. How do you implement graceful shutdown?
30. How do you implement blue-green deployment?
31. How do you handle config changes without redeployment?
32. How do you handle secret rotation?
33. How do you handle database migration safely?
34. How do you handle backward compatibility?
35. How do you handle breaking API changes?
36. How do you handle high CPU usage in production?
37. How do you handle high memory usage in production?
38. How do you handle high API latency?
39. How do you handle database connection pool exhaustion?
40. How do you handle thread pool exhaustion?
41. How do you handle Kafka consumer lag?
42. How do you handle Redis cache failure?
43. How do you handle Elasticsearch downtime?
44. How do you handle downstream service timeout?
45. How do you handle duplicate records in database?
46. How do you handle duplicate Kafka events?
47. How do you handle eventual consistency?
48. How do you handle data reconciliation?
49. How do you handle audit and compliance requirements?
50. How do you handle production incident communication?

---

## 18. Spring Boot and REST API Questions

1. How does Spring Boot auto-configuration work?
2. What is dependency injection?
3. Difference between constructor injection and field injection.
4. Difference between `@Component`, `@Service`, and `@Repository`.
5. Difference between `@Controller` and `@RestController`.
6. What is Spring Bean lifecycle?
7. What is singleton scope?
8. What is prototype scope?
9. What is `@Transactional`?
10. How does Spring transaction management work?
11. What is global exception handling?
12. How do you create custom exceptions?
13. How do you validate request body?
14. What is `@Valid`?
15. What is `@ControllerAdvice`?
16. What is filter?
17. What is interceptor?
18. Difference between filter and interceptor.
19. How do you secure REST APIs?
20. How do you implement pagination?
21. How do you implement sorting?
22. How do you implement API versioning?
23. What is DTO?
24. Why should we not expose entity directly?
25. How do you handle CORS?
26. How do you handle file upload?
27. How do you handle large payloads?
28. How do you handle timeout in RestTemplate or WebClient?
29. Difference between RestTemplate and WebClient.
30. How do you test Spring Boot APIs?

---

## 19. Core Java Questions

1. What are OOP principles?
2. Difference between abstraction and encapsulation.
3. Difference between interface and abstract class.
4. What is polymorphism?
5. What is method overloading?
6. What is method overriding?
7. Difference between `==` and `equals`.
8. Difference between `String`, `StringBuilder`, and `StringBuffer`.
9. How does `HashMap` work internally?
10. Difference between `HashMap` and `ConcurrentHashMap`.
11. Difference between `ArrayList` and `LinkedList`.
12. Difference between `HashSet` and `TreeSet`.
13. Difference between checked and unchecked exception.
14. What is immutable class?
15. How do you create immutable class?
16. What is `final` keyword?
17. What is `static` keyword?
18. What is `volatile` keyword?
19. What is `synchronized` keyword?
20. Difference between `synchronized` and `Lock`.
21. What is deadlock?
22. How do you prevent deadlock?
23. What is thread pool?
24. What is ExecutorService?
25. What is CompletableFuture?
26. What is Stream API?
27. Difference between `map` and `flatMap`.
28. What is Optional?
29. What is functional interface?
30. What is lambda expression?

---

## 20. Scenario-based Questions

1. One user clicked payment button twice. How do you prevent double payment?
2. Order created successfully but payment failed. What will you do?
3. Payment success received but inventory failed. What will you do?
4. Kafka consumer processed message but failed before committing offset. What happens?
5. Elasticsearch is showing old product price. How do you debug?
6. API is taking 10 seconds in production. How do you debug?
7. Database CPU is high. How do you debug?
8. Kafka consumer lag is increasing. How do you debug?
9. Redis is down. How should your service behave?
10. Downstream service is slow. How do you protect your service?
11. One service is failing continuously. How do you prevent cascading failure?
12. User is not able to login. How do you debug?
13. API returns 500 but logs are not clear. What improvements will you add?
14. Duplicate events are coming from Kafka. How do you handle them?
15. Events are coming out of order. How do you handle them?
16. Order cancellation and shipment happened at same time. How do you fix it?
17. Inventory became negative. How do you debug and fix?
18. Search is slow in Elasticsearch. How do you optimize?
19. JVM memory is increasing continuously. How do you debug?
20. Full GC is happening frequently. How do you debug?
21. Application is not starting after deployment. How do you debug?
22. Database migration failed in production. What will you do?
23. New API version broke old clients. How do you prevent this?
24. Circuit breaker is always open. How do you debug?
25. Retry is creating too much load. How do you fix it?
26. Payment gateway callback came late. How do you handle it?
27. Customer says payment deducted but order failed. How do you debug?
28. Kafka topic has too many failed messages. How do you handle it?
29. How do you reprocess failed messages safely?
30. How do you design a production-ready microservice from scratch?
