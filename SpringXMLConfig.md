## 🧩 Understanding ApplicationContext vs BeanFactory in Spring (XML-based Configuration)

In Spring, the **IoC container** is responsible for creating, managing, and injecting beans (objects). There are two primary ways to access this container:

### 1️⃣ **BeanFactory**

* The older interface for accessing the Spring container.
* It **lazily initializes beans** (objects are created only when needed).
* Now **deprecated** in most use cases.

### 2️⃣ **ApplicationContext**

* The **modern and more powerful** interface for Spring containers.
* It is a **superset of `BeanFactory`** and includes many additional features like internationalization, event propagation, and more.
* Beans are **eagerly initialized** (created as soon as the container is loaded).

---

## 🏗️ Ways to Configure the Spring Container

Spring provides multiple ways to configure and build the application context:

1. **XML Configuration** (legacy but still used)
2. **Java-based Configuration** (using `@Configuration` classes)
3. **Annotation-based Configuration** (using `@ComponentScan`, `@Component`, etc.)

### 📌 In this section, we'll focus on the **XML-based configuration**.

---

## 📁 Loading the Container using XML

To create and configure the Spring IoC container using an XML file, you can do:

```java
ApplicationContext context = new ClassPathXmlApplicationContext("file.xml");
```

### 📝 What happens here?

* This line **creates the ApplicationContext** (container).
* It **reads the configuration** from `file.xml` located in the `resources` folder.
* All the `<bean>` tags in the XML file **define which classes should be managed** by Spring.
* The beans (objects) for all those classes are **instantiated immediately** when this line is executed (eager initialization).

---

## 🧱 Structure of `file.xml`

Here’s a basic structure of the Spring XML configuration file:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
           http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="engine" class="com.example.Engine"/>
    <bean id="car" class="com.example.Car"/>

</beans>
```

### 📌 Notes:

* The **XSD schema** (XML Schema Definition) defines the rules for valid XML tags.
* Each `<bean>` tag represents a class that Spring should manage.
* The `id` is used to uniquely identify a bean.
* The `class` specifies the **fully qualified class name**.

---

## 🤔 What if I define the same class multiple times?

If you define the same class multiple times in the XML (even with different IDs), **Spring will create that many separate objects** (one per `<bean>` tag).

---

## ❓ If `id` is Optional, How Do You Access the Bean?

If you don’t specify an `id`, Spring will **automatically generate one** based on the class name (usually using the **uncapitalized class name**).

But best practice is to **always provide an explicit `id`**, especially when using `context.getBean("beanId")`.

---

## 🛠 Getting a Bean from the Container

Once the container is created:

```java
Car car = (Car) context.getBean("car");
```

Spring will return the instance of `Car` as configured in the XML.

---

## ⚠️ Important: Eager Initialization

In XML-based configuration with `ApplicationContext`, beans are **created as soon as the container is initialized**, **not** when `getBean()` is called.

This is different from `BeanFactory`, which uses **lazy initialization**.

---

## 🧪 Example

### XML: `resources/applicationContext.xml`

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans 
       http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="engine" class="com.example.Engine"/>
    <bean id="car" class="com.example.Car"/>
</beans>
```

### Java:

```java
public class MainApp {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");

        Car car = (Car) context.getBean("car");
        car.drive();
    }
}
```
