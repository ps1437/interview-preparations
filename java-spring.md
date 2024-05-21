
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
   - How does Spring handle circular dependencies between beans?
   - Explain the use of profiles in Spring configuration.

2. **Annotations and Configuration**
   - What are stereotype annotations (e.g., @Service, @Component, @Repository)?
   - What is the difference between @Component, @Repository, and @Service?
   - What are some important Spring annotations you have used?
   - Explain the use of @Configuration and @Bean annotations.

3. **Dependency Injection**
   - Explain bean wiring and the @Autowired annotation.
   - What is the difference between @Qualifier and @Primary?
   - What are the different types of dependency injection in Spring?
   - How does constructor injection differ from setter injection?

4. **Spring MVC**
   - What is the DispatcherServlet?
   - How to handle exceptions in the Spring MVC framework?
   - Explain the use of @RequestMapping and @GetMapping annotations.
   - How do you handle form submission in Spring MVC?

5. **Spring AOP**
   - What is Aspect-Oriented Programming (AOP) in Spring?
   - Basic concepts of AOP such as Aspect, Advice, Pointcut, and JoinPoint.
   - Explain the different types of advice in Spring AOP (e.g., Before, After, Around).

6. **Spring Boot**
   - What is Spring Boot? Why should you use it?
   - What is the difference between Spring Boot and Spring MVC?
   - Explain the @SpringBootApplication and @EnableAutoConfiguration annotations.
   - What is Spring Boot Actuator and its advantages?
   - How do you configure external properties in Spring Boot?

7. **Spring Data**
   - What is JpaRepository?
   - Benefits of JPA Repository.
   - What is the @Transactional annotation?
   - Different transaction strategies in Spring.
   - How do you use the CrudRepository and PagingAndSortingRepository interfaces?

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
   - Difference between RestTemplate and Feign Client.
   - How do microservices communicate with each other?
   - What is the role of API Gateway in microservices?

3. **Spring Cloud**
   - Explain Spring Cloud and its modules.
   - What is an API Gateway?
   - What is a Discovery Service?
   - Explain the concept of service registry and discovery.

4. **Resilience and Monitoring**
   - What is circuit breaker pattern and how to implement it?
   - How to achieve centralized logging in microservices?
   - What is trace ID?
   - How do you implement distributed tracing in microservices?

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
