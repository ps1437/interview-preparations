## DSA 
### https://leetcode.com/problems/path-sum/description/
### https://leetcode.com/problems/maximum-product-after-k-increments/description/



---

# 🚀 Design a Spring Boot API for Dynamic Filtering (Generic Query Builder - Same quesiton for UI compoennt also

## ❓ Problem Statement
Design an API in Spring Boot that will:
- Accept **generic filters** from the client (via request body or query params).
- Dynamically build a query based on those filters.
- Return matching results.

The API should be flexible enough to support different operators like:
- Equals (`eq`)
- Not Equals (`ne`)
- Greater Than (`gt`)
- Less Than (`lt`)
- Like (`like`)
- In (`in`)

---

## ✅ Example Input
```json
POST /users/search
{
 {
  "filters": [
    { "field": "name", "operator": "like", "value": "Raj" },
    { "field": "age", "operator": "gt", "value": 25 },
    { "field": "city", "operator": "in", "value": ["Delhi", "Mumbai"] }

  ],
  "sort": { "field": "age", "direction": "desc" },
  "page": 1,
  "size": 10
}

}
````

---

## ✅ Example Output

```json
[
  {
    "id": 1,
    "name": "Rajkumar",
    "age": 30,
    "city": "Delhi"
  }
]
```

---

## ⚙️ API Design

### 1. Controller

```java
@RestController
@RequestMapping("/users")
@RequiredArgsConstructor
public class UserController {

    private final UserService userService;

    @PostMapping("/search")
    public List<User> search(@RequestBody FilterRequest filterRequest) {
        return userService.searchUsers(filterRequest.getFilters());
    }
}
```

---

### 2. DTOs

```java
@Data
public class FilterRequest {
    private List<FilterCriteria> filters;
}

@Data
@AllArgsConstructor
@NoArgsConstructor
public class FilterCriteria {
    private String field;
    private String operator; // eq, ne, gt, lt, like, in
    private Object value;
}
```

---

### 3. Service Layer

```java
@Service
@RequiredArgsConstructor
public class UserService {

    private final UserRepository userRepository;

    public List<User> searchUsers(List<FilterCriteria> filters) {
        Specification<User> spec = buildSpecification(filters);
        return userRepository.findAll(spec);
    }

    private Specification<User> buildSpecification(List<FilterCriteria> filters) {
        return (root, query, builder) -> {
            List<Predicate> predicates = new ArrayList<>();

            for (FilterCriteria filter : filters) {
                String field = filter.getField();
                Object value = filter.getValue();

                switch (filter.getOperator().toLowerCase()) {
                    case "eq":
                        predicates.add(builder.equal(root.get(field), value));
                        break;
                    case "ne":
                        predicates.add(builder.notEqual(root.get(field), value));
                        break;
                    case "gt":
                        predicates.add(builder.greaterThan(root.get(field), (Comparable) value));
                        break;
                    case "lt":
                        predicates.add(builder.lessThan(root.get(field), (Comparable) value));
                        break;
                    case "like":
                        predicates.add(builder.like(root.get(field), "%" + value + "%"));
                        break;
                    case "in":
                        CriteriaBuilder.In<Object> inClause = builder.in(root.get(field));
                        ((List<?>) value).forEach(inClause::value);
                        predicates.add(inClause);
                        break;
                }
            }

            return builder.and(predicates.toArray(new Predicate[0]));
        };
    }
}
```

---

### 4. Repository

```java
@Repository
public interface UserRepository extends JpaRepository<User, Long>, JpaSpecificationExecutor<User> {
}
```

---

## ⏳ Complexity

* Building specification: **O(N)** (N = number of filters).
* Query execution: **Depends on DB** (SQL optimizer).
* Flexible & extendable.

👉 Do you want me to also add **sorting + pagination design** in the same `.md` file so you have a **complete API spec**?
```
