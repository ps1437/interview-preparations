## Pepsico interview experiance for Senior Assoicte Enginner Manager rol

1. Design Recommendation System for OTT platform
2. Project ARch
3. NO SQL vs SQL
4. Featrue flag ( Enable featurea based on role)
5. Secuity ( JWT/ Ouaht/RoleBased and realtime implimentation)
6. BFF VS Aggregator pattern
7. Components of Project ( Gateway/LoadBalnacer/Intenral application/Externla/Vendor application)
8. Optimized performance of An Application
9. What is Idempotency
10. How Kakfa Insure semantic consistency
11. Idemponentcy proeprty in kafka

## Guess the OP

```java
List<String> names = List.of("Alice", "Bob", "Charlie");
 
names.stream()
     .map(name -> {
         System.out.println("Mapping: " + name);
         return name.toUpperCase();
     })
     .limit(1)
     .forEach(System.out::println);
```
What is the output and how many times is map() executed?



```java
@Service

public class OrderService {
 
    @Transactional
    public void placeOrder() {

        saveOrder(); // transactional

        throw new RuntimeException("Simulate failure");

    }
 
    public void saveOrder() {

        // DB save logic

        System.out.println("Order saved.");

    }

}
```
 Will the DB changes in saveOrder() be rolled back?

 

