## ✅ What is Fetching in JPA/Hibernate?

When your entity has relationships (like `@OneToMany`, `@ManyToOne`, `@OneToOne`, etc.), Hibernate needs to decide **when to load the related data**:

* **Eager Fetching**: Load related data immediately along with the parent entity.
* **Lazy Fetching**: Load related data **only when it is accessed**, not at the time of initial entity retrieval.

---

## ✅ Lazy vs Eager Fetching: Key Differences

| Feature                 | Lazy Fetching                      | Eager Fetching                            |
| ----------------------- | ---------------------------------- | ----------------------------------------- |
| **When data is loaded** | When accessed                      | Immediately when the parent is fetched    |
| **Performance**         | Better (fewer DB calls initially)  | Potentially worse (more DB joins/queries) |
| **Use case**            | Large datasets, rarely needed data | Always-used data                          |
| **Default in JPA**      | `@OneToMany`, `@ManyToMany` → Lazy | `@ManyToOne`, `@OneToOne` → Eager         |
| **Can cause**           | LazyInitializationException        | Over-fetching (unnecessary joins)         |

---

## ✅ Default Fetch Types for Relationships

| Relationship Type | Default Fetch Type |
| ----------------- | ------------------ |
| `@OneToOne`       | `EAGER`            |
| `@ManyToOne`      | `EAGER`            |
| `@OneToMany`      | `LAZY`             |
| `@ManyToMany`     | `LAZY`             |

---

## ✅ How to Set Fetch Type Explicitly

You can control the fetching behavior using the `fetch` attribute in the relationship annotation:

### 🔹 Example: `@OneToMany` with Lazy Fetching (default)

```java
@OneToMany(mappedBy = "department", fetch = FetchType.LAZY)
private List<Employee> employees;
```

### 🔹 Example: `@OneToMany` with Eager Fetching

```java
@OneToMany(mappedBy = "department", fetch = FetchType.EAGER)
private List<Employee> employees;
```

---

## ✅ Practical Example in Spring Boot

### 🧩 Entities

#### `Department.java`

```java
@Entity
public class Department {

    @Id
    @GeneratedValue
    private Long id;

    private String name;

    @OneToMany(mappedBy = "department", fetch = FetchType.LAZY)
    private List<Employee> employees;
}
```

#### `Employee.java`

```java
@Entity
public class Employee {

    @Id
    @GeneratedValue
    private Long id;

    private String name;

    @ManyToOne(fetch = FetchType.EAGER) // default
    @JoinColumn(name = "department_id")
    private Department department;
}
```

---

## ✅ How Lazy Loading Works in Spring Boot

Spring Boot uses **Hibernate** as the default JPA provider.

* When you load a `Department`, the `employees` list is **not fetched** from the database right away (because it's `LAZY`).
* It is fetched **only when you explicitly access it**, e.g., `department.getEmployees()`.

Hibernate uses a **proxy object** behind the scenes for lazy fields. When the proxy is accessed, it triggers the actual SQL query.

---

## 🚨 LazyInitializationException

### 🔥 What is it?

This exception is thrown when:

* You try to access a lazily loaded collection **outside the persistence context (after the session is closed)**.

### 🧠 Example:

```java
@RestController
public class DepartmentController {

    @Autowired
    private DepartmentRepository departmentRepo;

    @GetMapping("/departments/{id}")
    public Department getDept(@PathVariable Long id) {
        Department dept = departmentRepo.findById(id).get();
        dept.getEmployees().size(); // May throw LazyInitializationException!
        return dept;
    }
}
```

If the session is closed before accessing `getEmployees()`, you'll get an exception.

---

## ✅ Solutions to LazyInitializationException

### 1. **Use `@Transactional`**

Ensure the fetching happens **within a transaction** (i.e., session is open):

```java
@Service
public class DepartmentService {

    @Autowired
    private DepartmentRepository departmentRepo;

    @Transactional
    public Department getDeptWithEmployees(Long id) {
        Department dept = departmentRepo.findById(id).get();
        dept.getEmployees().size(); // Will work inside transaction
        return dept;
    }
}
```

### 2. **Use Eager Fetching (Not always recommended)**

```java
@OneToMany(mappedBy = "department", fetch = FetchType.EAGER)
```

This loads all employees every time a department is loaded, **even if not needed**.

### 3. **Use DTO Projections**

Instead of loading full entities, use a custom DTO in your JPQL or native query:

```java
@Query("SELECT new com.example.dto.DepartmentDto(d.name, e.name) FROM Department d JOIN d.employees e WHERE d.id = :id")
List<DepartmentDto> getDepartmentWithEmployees(@Param("id") Long id);
```

### 4. **Use `@EntityGraph` (Spring Data JPA)**

A clean way to force fetch relationships:

```java
@EntityGraph(attributePaths = {"employees"})
Optional<Department> findById(Long id);
```

---

## ✅ Best Practices

| Use Case                           | Recommended Fetch Type                        |
| ---------------------------------- | --------------------------------------------- |
| Frequently used, small collections | `EAGER`                                       |
| Large or optional collections      | `LAZY`                                        |
| JSON Serialization (REST APIs)     | DTOs or `@JsonIgnore` to avoid infinite loops |

---

## ✅ Extra Notes for REST APIs

When exposing entities through REST controllers:

* **Avoid exposing entities directly** — use **DTOs** instead.
* Lazy collections can cause **infinite recursion** or **performance issues**.
* To avoid this, consider:

  * Using `@JsonIgnore` or `@JsonManagedReference/@JsonBackReference`
  * Using `ModelMapper` or `MapStruct` to convert entities to DTOs.

---

## ✅ Summary

| Concept                              | Lazy                                 | Eager                           |
| ------------------------------------ | ------------------------------------ | ------------------------------- |
| Default for `@OneToMany`             | ✅ Yes                                | ❌ No                            |
| Performance                          | ✅ Better (if data not always needed) | ❌ Can cause over-fetching       |
| Causes `LazyInitializationException` | Yes, outside session                 | No                              |
| Use Case                             | Large collections, on-demand         | Small, always-used associations |
| Safer for APIs                       | ❌ No, unless managed properly        | ⚠️ Safer but less efficient     |
