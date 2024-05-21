

# Java Spring Interview Questions

## Core Java



1. **String Handling**
   - What are different ways to create a String object?
   - Why is String immutable or final in Java?
   - What is the String Pool?
   - How do you compare two strings in Java?
   - Explain `StringBuilder` and `StringBuffer`. When should each be used?
   
   Reference : String (https://medium.com/nerd-for-tech/concept-of-string-pool-and-string-object-creation-in-java-27ed2b3089f5)

2. **Object-Oriented Programming**
   - Why doesn’t Java support multiple inheritance?
   - What is the difference between overloading and overriding (Dynamic and compile time polymorphism) ?
   - What is the purpose of the `final`, `static`, `finally`, `finalize`, `this`, `super` keywords?
   - What is an abstract class, and how does it differ from an interface?
   - Can we create an object for an interface?

3. **Memory Management**
   - What is the difference between heap and stack memory?
   - Explain garbage collection in Java. How does it work?
   - What are memory leaks, and how can they be prevented in Java?
   - What are the different parts of Heap ( old and the new generation etc)
   - What algorithm JVM used to clean up memory (mark & sweep)

4. **Collections Framework**
   - How does HashMap work in Java?
   - What is the importance of `hashCode()` and `equals()` methods?
   - What are the differences between HashMap, TreeMap, Hashtable, and LinkedHashMap?
   - What is the difference between ArrayList and LinkedList?
   - How can we create a synchronized collection from a given collection?
   - Explain the difference between Set and List in Java.
   - How does ConcurrentHashMap work in Java?
   - What is the difference between fail-fast and fail-safe iterators?
   - How to use custome key in Map ( equals and hashcode implimentation)
   - what is hash collision in java?
   - What are the performance characteristics of ArrayList and LinkedList for operations like insertion, deletion, and retrieval?

   Reference : [Collections] (https://medium.com/@harendrakumarrajpoot5/top-50-java-collections-interview-questions-you-need-to-know-e55fcdc8dbfb)

5. **Exception Handling**
   - What is the difference between checked and unchecked exceptions?
   - Create a Custom Exception
   - Try , Catch & finally
   - When Finally block is not executed ( System.exit(), Infinite loop or system crush)
   - How does the try-with-resources statement work?
   - Explain the purpose of the `throw` and `throws` keywords.

6. **Threads and Concurrency**
   - Different ways to create a thread.
   - Explain Java Thread Safety and Synchronization.
   - What is the difference between `notify()` and `notifyAll()`?
   - What are the different states of a thread lifecycle?
   - What is the difference between `Runnable` and `Callable` in Java?
   - Sleep vs wait

7. **Design Patterns**
   - Explain different design patterns you have worked with, such as Singleton, Factory, Prototype, Proxy, and Builder.
   - Singleton - How can we break Singleton design pattern

8. **Miscellaneous**
   - What is an immutable class? How to create an immutable class?
   - How to avoid deadlock in Java?
   - What is the Just-In-Time (JIT) compiler?
   - Explain the concept of Serialization and Deserialization in Java.
   - What is Externalization
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

   ref : Prototype scope ( https://apurvsheth.medium.com/spring-boot-bean-scope-is-prototype-really-a-prototype-b7c32e05ce89)

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
   - What is the purpose of the @ResponseBody annotation in Spring MVC?
   - Explain the concept of Interceptors in Spring MVC and when to use them.
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
   - How Springboot work internally ?
   - What is Spring Boot Actuator, and what are its advantages?
   - How do you configure external properties in Spring Boot?
   - What is the purpose of the @RestController annotation in Spring Boot?
   - Explain the Spring Boot Starter concept and its benefits.
   - What are Spring Boot Profiles, and how are they used?
   - How does Spring Boot handle dependency management?
   - What is the role of embedded servers in Spring Boot and type of webserver its supports?

7. **Spring Data**
   - What is JpaRepository, and how does it differ from other Spring Data repositories?
   - What are the benefits of using JPA Repository in Spring Data?
   - Explain the @Transactional annotation in the context of Spring Data.
   - Explain Propogation and Isolation level
   - What are the different transaction strategies available in Spring Data?
   - How do you use the CrudRepository and PagingAndSortingRepository interfaces in Spring Data?
   - What are the advantages of using Specifications in Spring Data JPA?

   Ref : Transcation Propoagtion (https://www.youtube.com/watch?v=IVHcWTegWyM&t=2574s&pp=ygUccHJvcG9nYXRpb24gbGV2ZWwgc3ByaW5nYm9vdA%3D%3D)
         Transction Isolation  (https://www.youtube.com/watch?v=7rNiIavYB2w&t=1052s)

8. **Miscellaneous**
   - How to configure a Spring web application?
   - What is the difference between @PathVariable and @RequestParam?
   - How to handle null conditions for path variables before reaching the controller?
  

9. **Spring security**
   - How to enable securtiy in sprinboot
   - What are authentication and authorization in Spring Security?
   - JWT based Authentication
   - What is JWT , What is contains
   - What is Claim in JWT and Types of claims.
   - Token vs Access token vs Refresh Token
   - What are the key components of Spring Security?
   - How to implement a custom filter in Spring Security?
   - What do @Secured and @RolesAllowed do? What is the difference between them?
   - Difference between hashing and encryption ( password- always hashed )


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
   - What role does a service registry like Netflix Eureka play in microservices communication?
   - Explain the concept of event-driven architecture in the context of microservices communication.
   - What is the purpose of API versioning in microservices?
   - How do microservices handle security and authentication during communication?

3. **Spring Cloud**
   - Spring Cloud Modules: Explanation and examples (Netflix OSS, Spring Cloud Config, Spring Cloud Netflix, Spring Cloud Sleuth).
   - API Gateway in Microservices: Purpose and comparison of Spring Cloud Gateway with Zuul.
   - Discovery Service: Benefits for dynamic service registration and discovery in microservices.
   - Service Registry and Discovery using Netflix Eureka: Explanation within Spring Cloud.
   - Client-Side Load Balancing: Functionality and usage in microservices with Spring Cloud.
   - Spring Cloud Config: Role in externalized configuration management for microservices.
   - Spring Cloud Netflix: Features like service discovery, circuit breakers, and routing.
   - Spring Cloud Sleuth: Use in distributed tracing for microservices.
   - Distributed Configuration Management: Purpose and differences from traditional approaches in Spring Cloud.
   - Fault Tolerance and Resilience in Spring Cloud: Handling in microservices communication.

4. **Resilience and Monitoring**
   - What is the Circuit Breaker pattern, and how does it help in building resilient microservices? 
   - How can you implement the Circuit Breaker pattern in Spring Boot applications using libraries like Netflix Hystrix or Resilience4j?
   - Explain the concept of centralized logging in microservices, and what are the benefits of using tools like ELK Stack (Elasticsearch, Logstash, Kibana) or Splunk?
   - What is a Trace ID & Span Id, and how does it help in distributed tracing for monitoring microservices interactions?
   - How do you implement distributed tracing in microservices using tools like Zipkin or Jaeger? Rate your experience with distributed tracing.
   - Explain the concept of health checks in microservices, and how can you implement them using Spring Boot Actuator? 
   - How can you monitor the performance and availability of microservices in production environments using tools like Prometheus and Grafana? Rate your experience with using monitoring tools.
   - What role do alerting and notifications play in microservices monitoring, and how can you set up alerting rules using tools like Prometheus Alertmanager or Grafana?


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

   Reference : Micro service design pattern [https://medium.com/capital-one-tech/10-microservices-design-patterns-for-better-architecture-befa810ca44e]
       Implimentation example [https://www.vinsguru.com/category/design-pattern/]

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


*Ref: Core java (https://www.journaldev.com/2366/core-java-interview-questions-and-answers)
*Ref : Microservices, Design pattern, Kafka (https://www.vinsguru.com/)

