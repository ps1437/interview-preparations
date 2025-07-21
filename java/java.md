

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
   - Volatile vs Atomatic variable with example
   - Type of Locks ( ReadWrite vs ReentrantLock)
   - What are daemon threads?
   - Difference between ReentrantLock and synchronized block.
   - Executor service vs Thread
   - ConcurrentHashMap vs Collections.synchronizedMap()
   - CountDownLatch vs cyclicbarrier
   - Sleep vs wait

7. **Design Patterns**
   - Explain different design patterns you have worked with, such as Singleton, Factory, Prototype, Proxy, and Builder.
   - Singleton - How can we break Singleton design pattern ( static class approach must)
   - Publisher/Subscriber (Pub/Sub) and Observer

8. **Miscellaneous**
   - What is an immutable class? How to create an immutable class?
   - How to avoid deadlock in Java?
   - What is the Just-In-Time (JIT) compiler?
   - Explain the concept of Serialization and Deserialization in Java.
   - What is Externalization
   - What is a Java ClassLoader, and what are its types?
   - How does the var keyword work in Java 10?
   - Switch Expressions
   - Records classes
   - Seailed classes
   - Text Blocks 
   - Pattern Matching for instanceof
   
9. **Java 8**
   - What are lambda expressions, and how are they used in Java 8?
   - What is the Streams API in Java 8, and what are its benefits?
   - Explain the difference between map and flatMap in Streams.
   - What are the different phases of a Stream ( stream -> Intermediator operation -> ternary Operation)
   - What is the difference between a sequential stream and a parallel stream?
   - Name some of the functional interfaces provided by Java 8.
   - What is a functional interface in Java 8?
   - What are default methods in interfaces, and why were they introduced in Java 8?
   - How can you implement a default method in an interface?
   - What is a static method in an interface, and how is it different from a default method?
   - How can you use Optional to avoid null checks in your code?
   - What are method references in Java 8?
   - Question about groupBy and Comparator chaining.
   - What is the CompletableFuture class in Java 8, and how does it improve asynchronous programming?
   - Future vs CompletableFuture
   - Stream Vs Collection?
   - What is Spliterator?
   - How to create a Functional Interface
   


*Reference : 

[String Handling](https://medium.com/nerd-for-tech/concept-of-string-pool-and-string-object-creation-in-java-27ed2b3089f5)

[Collections Framework](https://medium.com/@harendrakumarrajpoot5/top-50-java-collections-interview-questions-you-need-to-know-e55fcdc8dbfb)

[Core Java](https://www.digitalocean.com/community/tutorials/core-java-interview-questions-and-answers)
