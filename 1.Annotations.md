## ✅ Basic Syntax to Define a Custom Annotation

```java
// Optional: these imports if you want special behavior
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import java.lang.annotation.Documented;

// Use these annotations to control how your annotation behaves
@Documented                      // (optional) shows up in Javadoc
@Target(ElementType.TYPE)       // where this annotation can be used (class, method, etc.)
@Retention(RetentionPolicy.RUNTIME) // how long annotation info is available (e.g., at runtime)

public @interface YourAnnotationName {
    // Define elements (like fields in a form)

    String author();              // required
    String date();                // required
    int version() default 1;      // optional with default value
    String[] reviewers();         // required, array type
}
```

---

## 🔍 What the Meta-Annotations Mean:

* `@Documented`: Makes it appear in generated Javadoc
* `@Target(...)`: Where this annotation can be used (class, method, field, etc.)

  * Common targets: `ElementType.TYPE`, `ElementType.METHOD`, `ElementType.FIELD`, etc.
* `@Retention(...)`: How long the annotation information is kept

  * `RetentionPolicy.SOURCE`: Only in source code
  * `RetentionPolicy.CLASS`: Stored in `.class` file but not available at runtime
  * `RetentionPolicy.RUNTIME`: Available at runtime using reflection (most useful)

---

## 🧪 Example: Define a Custom Annotation

```java
import java.lang.annotation.*;

@Documented
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
public @interface ClassInfo {
    String author();
    String date();
    int version() default 1;
    String[] reviewers();
}
```
