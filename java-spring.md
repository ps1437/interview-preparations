
```markdown
# Java Spring Interview Questions

## Core Java

1. **String Handling**
   - What are different ways to create a String object?
   - Why is String immutable or final in Java?
   - What is the String Pool?

2. **Object-Oriented Programming**
   - Why doesn’t Java support multiple inheritance?
   - What is the difference between overloading and overriding?
   - What is the purpose of the `final`, `static`, `finally`, `finalize`, `this`, `super` keywords?

3. **Memory Management**
   - What is the difference between heap and stack memory?

4. **Collections Framework**
   - How does HashMap work in Java?
   - What is the importance of `hashCode()` and `equals()` methods?
   - What are the differences between HashMap, TreeMap, Hashtable, and LinkedHashMap?
   - What is the difference between ArrayList and LinkedList?
   - How can we create a synchronized collection from a given collection?

5. **Exception Handling**
   - What is the difference between checked and unchecked exceptions?

6. **Threads and Concurrency**
   - Different ways to create a thread.
   - Explain Java Thread Safety and Synchronization.
   - What is the difference between `notify()` and `notifyAll()`?

7. **Design Patterns**
   - Explain different design patterns you have worked with, such as Singleton, Factory, Prototype, Proxy, and Builder.

8. **Miscellaneous**
   - What is an immutable class? How to create an immutable class?
   - How to avoid deadlock in Java?
   - What is the Just-In-Time (JIT) compiler?

## Spring Framework

1. **Introduction to Spring**
   - What are some important features and advantages of the Spring Framework?
   - What is the Spring IOC Container?
   - What is a Spring Bean?

2. **Annotations and Configuration**
   - What are stereotype annotations (e.g., @Service, @Component, @Repository)?
   - What is the difference between @Component, @Repository, and @Service?
   - What are some important Spring annotations you have used?

3. **Dependency Injection**
   - Explain bean wiring and the @Autowired annotation.
   - What is the difference between @Qualifier and @Primary?

4. **Spring MVC**
   - What is the DispatcherServlet?
   - How to handle exceptions in the Spring MVC framework?

5. **Spring AOP**
   - What is Aspect-Oriented Programming (AOP) in Spring?
   - Basic concepts of AOP such as Aspect, Advice, Pointcut, and JoinPoint.

6. **Spring Boot**
   - What is Spring Boot? Why should you use it?
   - What is the difference between Spring Boot and Spring MVC?
   - Explain the @SpringBootApplication and @EnableAutoConfiguration annotations.
   - What is Spring Boot Actuator and its advantages?

7. **Spring Data**
   - What is JpaRepository?
   - Benefits of JPA Repository.
   - What is the @Transactional annotation?
   - Different transaction strategies in Spring.

8. **Miscellaneous**
   - How to configure a Spring web application?
   - What is the difference between @PathVariable and @RequestParam?
   - How to handle null conditions for path variables before reaching the controller?

## SQL

1. **Basic Queries**
   - Find the 3rd highest salary in a table.
   - Explain the differences between primary key, foreign key, and composite key.

2. **Advanced Queries**
   - Find the department ID which is not having any employees.
   - Find the count of employees in each department.

3. **Stored Procedures and Functions**
   - What is the difference between a procedure and a function?
   - What is an index and why is it used?

## Microservices

1. **Fundamentals**
   - What is a microservice?
   - Principles/rules to follow while creating microservices.

2. **Communication**
   - Difference between RestTemplate and Feign Client.
   - How do microservices communicate with each other?

3. **Spring Cloud**
   - Explain Spring Cloud and its modules.
   - What is an API Gateway?
   - What is a Discovery Service?

4. **Resilience and Monitoring**
   - What is circuit breaker pattern and how to implement it?
   - How to achieve centralized logging in microservices?
   - What is trace ID?

5. **Security**
   - How to secure microservices?
   - What is OAuth2 and the various ways to implement it?
   - What is JWT and how does it work?

6. **Configuration and Scaling**
   - How to achieve centralized configuration using Config Server?
   - What is load balancing in microservices?
   - Explain database per microservice design pattern.

## Hibernate

1. **Basics**
   - What is Hibernate and its advantages over JDBC?
   - What is the difference between getCurrentSession() and openSession()?

2. **Caching**
   - What is Hibernate caching? Explain the first and second level cache.
   - How to configure Hibernate second level cache using EHCache?

3. **Mapping and Associations**
   - Explain different types of associations in Hibernate (One-to-One, One-to-Many, Many-to-Many).
   - What is the difference between @Entity and @Table?

4. **Miscellaneous**
   - How to solve the n+1 select problem in Hibernate?
   - What is the difference between session.flush() and session.clear()?
   - Explain @Version and @Audit annotations.

---

*Ref: (Core java ) [https://www.journaldev.com/2366/core-java-interview-questions-and-answers]
*Ref : (Microservices, Design pattern, Kafka ) [https://www.vinsguru.com/]
```
