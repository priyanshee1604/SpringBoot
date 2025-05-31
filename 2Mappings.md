# ✅ Hibernate Mapping Cheat Sheet

## ✅ 1. One-to-One Mapping

### Scenario:

Let's say we have two entities:

- `User`
- `Profile`

```java
@Entity
public class User {
    @Id
    private Long id;

    @OneToOne
    @JoinColumn(name = "profile_id") // foreign key
    private Profile profile;
}
````

### ✅ What happens:

* The `User` table gets a **foreign key column `profile_id`**.
* This column stores the `id` of the `Profile` entity.
* The relationship is **owned by the `User`** entity.

### 💡 Rule of Thumb:

* The side with the `@JoinColumn` annotation is the **owning side** (holds the foreign key).
* If you don’t specify `@JoinColumn`, Hibernate auto-generates a column like `profile_id`.

---

## ✅ 2. One-to-Many Mapping

### Scenario:

* `Department` has many `Employee`s.

```java
@Entity
public class Department {
    @Id
    private Long id;

    @OneToMany(mappedBy = "department")
    private List<Employee> employees;
}

@Entity
public class Employee {
    @Id
    private Long id;

    @ManyToOne
    @JoinColumn(name = "department_id")
    private Department department;
}
```

### ✅ What happens:

* The `Employee` table gets a **foreign key column `department_id`**.
* This column points to the `Department` entity.
* `@JoinColumn` is used on the `ManyToOne` side (owning side).

### 💡 Rule of Thumb:

* In `@OneToMany`, the foreign key is always in the **"many"** side's table.
* Use `mappedBy` to tell Hibernate **who owns the relationship** (i.e., `department` field in `Employee`).

---

## ✅ 3. Many-to-Many Mapping

### Scenario:

* A `Student` can enroll in many `Courses`.
* A `Course` can have many `Students`.

```java
@Entity
public class Student {
    @Id
    private Long id;

    @ManyToMany
    @JoinTable(
        name = "student_course",
        joinColumns = @JoinColumn(name = "student_id"),
        inverseJoinColumns = @JoinColumn(name = "course_id")
    )
    private List<Course> courses;
}
```

### ✅ What happens:

* A **new join table** `student_course` is created.
* It has two foreign key columns: `student_id` and `course_id`.

### 💡 Rule of Thumb:

* For `@ManyToMany`, you **must have a join table**.
* The side where `@JoinTable` is declared is the owning side.
* `@JoinColumn` specifies **which column holds the foreign key to which entity**.

---

## ✅ Summary of Where the Foreign Key Goes

| Mapping Type | Foreign Key Is In   | `@JoinColumn` Used On |
| ------------ | ------------------- | --------------------- |
| One-to-One   | Owning side’s table | On owning side        |
| One-to-Many  | "Many" side's table | On `@ManyToOne` side  |
| Many-to-Many | Separate join table | Inside `@JoinTable`   |

---

## 🎯 What's the Use of `@JoinColumn`?

* It **specifies the name of the foreign key column** in the database.
* Without it, Hibernate auto-generates a name (e.g., `profile_id`, `user_id`, etc.)
* It is used on the **owning side** of the relationship.

### Example:

```java
@JoinColumn(name = "profile_id")
```

* Means: "Use a column named `profile_id` to store the foreign key."

---

## ✅ Bonus Tip: `mappedBy`

* `mappedBy` is used on the **inverse (non-owning) side** to point to the field in the owning side.
* Tells Hibernate **not to create another join column**.
