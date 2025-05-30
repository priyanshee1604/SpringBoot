## ✅ Hibernate Caching Explained

### 🔁 What is Caching?

Caching in Hibernate is a powerful mechanism to reduce the number of database queries and improve application performance. When you insert or fetch data, Hibernate can temporarily store it in memory. If you access the same data again within the appropriate scope, Hibernate will serve it from the cache instead of querying the database again.

---

## ✅ Level 1 Cache (Session Cache)

### 💡 What is it?

* Level 1 Cache is **enabled by default** in Hibernate.
* It is **associated with the Hibernate `Session` object**.
* It exists **only within a single session** — when the session is closed, the cache is lost.

### 🔍 How it works:

* When you **insert or load an entity**, Hibernate stores it in the session cache.
* If you try to **read the same entity again within the same session**, Hibernate will fetch it from the cache, **not the database**.
* This avoids unnecessary SQL queries and improves performance.

### 🧠 Example:

```java
Session session = sessionFactory.openSession();

User user1 = session.get(User.class, 1L); // Hits the database
User user2 = session.get(User.class, 1L); // Comes from Level 1 cache
```

* In the above code, only **one query** will be executed for the user with `id = 1`.

### 🔒 Scope:

* **Scope:** Single session.
* **Invalidated:** When session is closed or cleared (`session.clear()` or `session.evict()`).

---

## ✅ Level 2 Cache (SessionFactory Cache)

### 💡 What is it?

* Level 2 Cache is **optional** and must be **explicitly enabled and configured**.
* It works at the **`SessionFactory` level**, meaning it is **shared across multiple sessions**.
* It allows cached data to persist **beyond the scope of a single session**.

### 🧠 How it works:

* When an entity is fetched and not found in the session cache, Hibernate checks the Level 2 cache.
* If it exists there, the entity is returned **without querying the database**.
* If not, it is fetched from the DB, returned to the session, and stored in both **Level 1 and Level 2** caches.

### 🔧 Common Providers:

To use Level 2 cache, you need a cache provider like:

* **EHCache**
* **Infinispan**
* **Hazelcast**
* **Redis** (via integration)
* **Caffeine**

### ⚙️ Example Configuration:

```xml
<property name="hibernate.cache.use_second_level_cache">true</property>
<property name="hibernate.cache.region.factory_class">org.hibernate.cache.ehcache.EhCacheRegionFactory</property>
```

```java
@Entity
@Cache(usage = CacheConcurrencyStrategy.READ_WRITE)
public class User {
    @Id
    private Long id;
    private String name;
}
```

### 🔒 Scope:

* **Scope:** Shared across sessions (application-wide).
* **Invalidated:** When entity is updated or evicted.

---

## 🧠 Summary of Caching Levels

| Level         | Scope          | Enabled By Default | Shared Across Sessions | When Used                 |
| ------------- | -------------- | ------------------ | ---------------------- | ------------------------- |
| Level 1 Cache | Per Session    | ✅ Yes              | ❌ No                   | Always (within a session) |
| Level 2 Cache | SessionFactory | ❌ No               | ✅ Yes                  | If configured explicitly  |

---

## 🏁 Final Notes

* Use Level 1 caching to avoid duplicate queries **within the same session**.
* Use Level 2 caching when you want to optimize reads **across sessions**, especially for frequently accessed, rarely changing data.
* Always choose a proper cache strategy (`READ_ONLY`, `READ_WRITE`, `NONSTRICT_READ_WRITE`, etc.) based on your data consistency requirements.
