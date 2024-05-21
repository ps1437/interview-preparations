
```markdown
# Java Spring Interview Questions

## Core Java

1. **String Handling**
   - What are different ways to create a String object?
   - Why is String immutable or final in Java?
   - What is the String Pool?
   - How do you compare two strings in Java?
   - Explain `StringBuilder` and `StringBuffer`. When should each be used?

2. **Object-Oriented Programming**
   - Why doesn’t Java support multiple inheritance?
   - What is the difference between overloading and overriding?
   - What is the purpose of the `final`, `static`, `finally`, `finalize`, `this`, `super` keywords?
   - What is an abstract class, and how does it differ from an interface?
   - Can we create an object for an interface?

3. **Memory Management**
   - What is the difference between heap and stack memory?
   - Explain garbage collection in Java. How does it work?
   - What are memory leaks, and how can they be prevented in Java?

4. **Collections Framework**
   - How does HashMap work in Java?
   - What is the importance of `hashCode()` and `equals()` methods?
   - What are the differences between HashMap, TreeMap, Hashtable, and LinkedHashMap?
   - What is the difference between ArrayList and LinkedList?
   - How can we create a synchronized collection from a given collection?
   - Explain the difference between Set and List in Java.
   - What is the difference between fail-fast and fail-safe iterators?

5. **Exception Handling**
   - What is the difference between checked and unchecked exceptions?
   - How does the try-with-resources statement work?
   - Explain the purpose of the `throw` and `throws` keywords.

6. **Threads and Concurrency**
   - Different ways to create a thread.
   - Explain Java Thread Safety and Synchronization.
   - What is the difference between `notify()` and `notifyAll()`?
   - What are the different states of a thread lifecycle?
   - What is the difference between `Runnable` and `Callable` in Java?

7. **Design Patterns**
   - Explain different design patterns you have worked with, such as Singleton, Factory, Prototype, Proxy, and Builder.
   - What is the Dependency Injection pattern, and how is it implemented in Spring?

8. **Miscellaneous**
   - What is an immutable class? How to create an immutable class?
   - How to avoid deadlock in Java?
   - What is the Just-In-Time (JIT) compiler?
   - Explain the concept of Serialization and Deserialization in Java.
   - What is a Java ClassLoader, and what are its types?

## Spring Framework

1. **Introduction to Spring**
   - What are some important features and advantages of the Spring Framework?
   - What is the Spring IOC Container?
   - What is a Spring Bean?
   - What are the different ways to configure a Spring bean?
   - What are the diferent scope in spring ( with Example )
   - Explain the Spring Bean lifecycle.
   - Explain the use of profiles in Spring configuration.
   - How to configure multiple datasource (in spring and springboot)

   ref : (Prototype scope) [https://apurvsheth.medium.com/spring-boot-bean-scope-is-prototype-really-a-prototype-b7c32e05ce89]

2. **Annotations and Configuration**
   - What are stereotype annotations (e.g., @Service, @Component, @Repository)?
   - What is the difference between @Component, @Repository, @Service.
   - What are some important Spring annotations you have used?
   - Explain the use of @Configuration and @Bean annotations.

3. **Dependency Injection**
   - Explain bean wiring and the @Autowired annotation.
   - How to Inject Prototype Bean.
   - What is the difference between @Qualifier and @Primary?
   - What are the different types of dependency injection in Spring?
   - How does constructor injection differ from setter injection?
   - How does Spring handle circular dependencies between beans?


4. **Spring MVC**
   - What is the purpose of the Model in Spring MVC?
   - Differentiate between @RequestParam and @PathVariable annotations in Spring MVC.
   - Explain the role of ViewResolver in Spring MVC.
   - What is the difference between Model.addAttribute() and ModelAndView in Spring MVC?
   - How does Spring MVC support internationalization and localization?
   - What is the purpose of the @ResponseBody annotation in Spring MVC?
   - Explain the concept of Interceptors in Spring MVC and when to use them.
   - What is the role of the RedirectAttributes interface in Spring MVC?
   - How does Spring MVC handle file uploads?
   - Explain the use of @Valid and @ModelAttribute annotations in form handling with Spring MVC.


5. **Spring AOP**
   - What is Aspect-Oriented Programming (AOP) in Spring?
   - Basic concepts of AOP such as Aspect, Advice, Pointcut, and JoinPoint.
   - Explain the different types of advice in Spring AOP (e.g., Before, After, Around).

6. **Spring Boot**
   - What is Spring Boot? Why should you use it?
   - What is the difference between Spring Boot and Spring MVC?
   - Explain the @SpringBootApplication and @EnableAutoConfiguration annotations in Spring Boot.
   - What is Spring Boot Actuator, and what are its advantages?
   - How do you configure external properties in Spring Boot?
   - What is the purpose of the @RestController annotation in Spring Boot?
   - Explain the Spring Boot Starter concept and its benefits.
   - What are Spring Boot Profiles, and how are they used?
   - How does Spring Boot handle dependency management?
   - What is the role of embedded servers in Spring Boot?

7. **Spring Data**
   - What is JpaRepository, and how does it differ from other Spring Data repositories?
   - What are the benefits of using JPA Repository in Spring Data?
   - Explain the @Transactional annotation in the context of Spring Data.
   - What are the different transaction strategies available in Spring Data?
   - How do you use the CrudRepository and PagingAndSortingRepository interfaces in Spring Data?
   - What is Spring Data JPA, and how does it simplify data access in Spring applications?
   - What is the purpose of Query Methods in Spring Data repositories?
   - Explain the concept of Entity Graphs in Spring Data JPA and when to use them.
   - What are the advantages of using Specifications in Spring Data JPA?
   - How does Spring Data handle pagination and sorting of data?

8. **Miscellaneous**
   - How to configure a Spring web application?
   - What is the difference between @PathVariable and @RequestParam?
   - How to handle null conditions for path variables before reaching the controller?
   - What is the role of the Spring Security module?

## SQL

1. **Basic Queries**
   - Find the 3rd highest salary in a table.
   - Explain the differences between primary key, foreign key, and composite key.
   - What are the different types of joins in SQL?
   - How do you write a query to retrieve duplicate records in a table?

2. **Advanced Queries**
   - Find the department ID which is not having any employees.
   - Find the count of employees in each department.
   - How do you perform a subquery in SQL?
   - Explain the use of the GROUP BY and HAVING clauses.

3. **Stored Procedures and Functions**
   - What is the difference between a procedure and a function?
   - What is an index and why is it used?
   - How do you handle transactions in SQL?
   - Explain the concept of triggers in SQL.

## Microservices

1. **Fundamentals**
   - What is a microservice?
   - Principles/rules to follow while creating microservices.
   - Explain the concept of bounded contexts in microservices architecture.

2. **Communication**
   - Difference between RestTemplate and Feign Client in microservices communication.
   - How do microservices typically handle asynchronous communication?
   - Explain the concept of service discovery in microservices architecture.
   - What are the benefits of using message brokers (e.g., Kafka, RabbitMQ) in microservices communication?
   - What is Circuit Breaker pattern, and how does it help in microservices communication resilience?
   - What are gRPC and Protocol Buffers, and how do they differ from RESTful communication in microservices?
   - What role does a service registry like Netflix Eureka play in microservices communication?
   - Explain the concept of event-driven architecture in the context of microservices communication.
   - What is the purpose of API versioning in microservices?
   - How do microservices handle security and authentication during communication?

3. **Spring Cloud**
   - Explain Spring Cloud and its various modules (e.g., Netflix OSS, Spring Cloud Config, Spring Cloud Netflix, Spring Cloud Sleuth).
   - What is the purpose of an API Gateway in a microservices architecture, and how does Spring Cloud Gateway differ from Zuul?
   - What is a Discovery Service, and how does it help in dynamic service registration and discovery in microservices?
   - Explain the concept of service registry and discovery using Netflix Eureka in Spring Cloud.
   - What is client-side load balancing, and how does it work in microservices with Spring Cloud?
   - How does Spring Cloud Config facilitate externalized configuration management in microservices?
   - What role does Spring Cloud Netflix play in providing features such as service discovery, circuit breakers, and intelligent routing?
   - Explain the use of Spring Cloud Sleuth for distributed tracing in microservices.
   - What is the purpose of distributed configuration management in Spring Cloud, and how does it differ from traditional configuration management approaches?
   -  How does Spring Cloud handle fault tolerance and resilience in microservices communication?

4. **Resilience and Monitoring**
   - What is the Circuit Breaker pattern, and how does it help in building resilient microservices? 
   - How can you implement the Circuit Breaker pattern in Spring Boot applications using libraries like Netflix Hystrix or Resilience4j?
   - Explain the concept of centralized logging in microservices, and what are the benefits of using tools like ELK Stack (Elasticsearch, Logstash, Kibana) or Splunk? Rate your familiarity with this topic.
   - What is a Trace ID, and how does it help in distributed tracing for monitoring microservices interactions?
   - How do you implement distributed tracing in microservices using tools like Zipkin or Jaeger? Rate your experience with distributed tracing.
   - What are metrics, and how are they used for monitoring microservices performance? Rate your familiarity with using metrics for monitoring.
   - Explain the concept of health checks in microservices, and how can you implement them using Spring Boot Actuator? Rate your knowledge on implementing health checks.
   - What is log aggregation, and how does it differ from centralized logging? Rate your understanding of log aggregation.
   - How can you monitor the performance and availability of microservices in production environments using tools like Prometheus and Grafana? Rate your experience with using monitoring tools.
   - What role do alerting and notifications play in microservices monitoring, and how can you set up alerting rules using tools like Prometheus Alertmanager or Grafana? Rate your familiarity with setting up alerts.


5. **Security**
   - How to secure microservices?
   - What is OAuth2 and the various ways to implement it?
   - What is JWT and how does it work?
   - Explain the concept of API security and best practices for securing APIs.

6. **Configuration and Scaling**
   - How to achieve centralized configuration using Config Server?
   - What is load balancing in microservices?
   - Explain database per microservice design pattern.
   - How do you implement service scaling in a microservices architecture?

## Hibernate

1. **Basics**
   - What is Hibernate and its advantages over JDBC?
   - What is the difference between getCurrentSession() and openSession()?
   - Explain the role of the Hibernate SessionFactory.

2. **Caching**
   - What is Hibernate caching? Explain the first and second level cache.
   - How to configure Hibernate second level cache using EHCache?
   - What are the benefits of using caching in Hibernate?

3. **Mapping and Associations**
   - Explain different types of associations in Hibernate (One-to-One, One-to-Many, Many-to-Many).
   - What is the difference between @Entity and @Table?
   - How do you implement inheritance mapping in Hibernate?

4. **Miscellaneous**
   - How to solve the n+1 select problem in Hibernate?
   - What is the difference between session.flush() and session.clear()?
   - Explain @Version and @Audit annotations.
   - What is the purpose of the @Lazy annotation in Hibernate?


*Ref: (Core java ) [https://www.journaldev.com/2366/core-java-interview-questions-and-answers]
*Ref : (Microservices, Design pattern, Kafka ) [https://www.vinsguru.com/]
```
