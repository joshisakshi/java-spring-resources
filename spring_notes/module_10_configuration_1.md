# Module 1 - Spring Fundamentals

# Chapter 10 - `@Configuration` & `@Bean`

## Part 1 — Java Configuration, Why `@Bean` Exists & Spring Internals

---

# Interview Frequency

⭐⭐⭐⭐⭐ (Extremely High)

Asked In:

- Amazon
- Microsoft
- Walmart
- Goldman Sachs
- JP Morgan
- Visa
- Atlassian
- Oracle

Common Questions

- Difference between `@Bean` and `@Component`
- Why do we need `@Bean`?
- What does `@Configuration` do?
- How does Spring process `@Configuration`?
- Can I replace everything with `@Component`?

---

# Goal

By the end of this chapter you should understand:

- What is Java Configuration?
- Why `@Configuration` was introduced
- What `@Bean` does
- Difference between `@Bean` and `@Component`
- Internal processing
- Production use cases
- Interview scenarios

---

# Table of Contents

1. The Problem
2. What is `@Configuration`?
3. What is `@Bean`?
4. `@Bean` vs `@Component`
5. Internal Startup Flow
6. Production Examples
7. Best Practices
8. Interview Questions

---

# 1. The Problem

Suppose you own this class.

```java
@Service
public class PaymentService {

}
```

Easy.

Simply write

```java
@Service
```

Spring discovers it.

---

Now consider a third-party library.

```java
public class StripeClient {

}
```

Can you modify it?

No.

It belongs to someone else.

So you cannot write:

```java
@Service
```

or

```java
@Component
```

What now?

Spring still needs to manage it.

---

# Solution

Instead of annotating the class,

tell Spring how to create it.

```
@Configuration

↓

@Bean

↓

Spring Manages Object
```

This is exactly why `@Bean` exists.

---

# 2. What is `@Configuration`?

## Interview Definition

`@Configuration` marks a class as a source of Spring bean definitions.

Think of it as a **Java-based replacement for the old XML configuration files**.

Instead of XML like:

```xml
<bean id="paymentService"
      class="com.example.PaymentService"/>
```

Spring allows you to write Java:

```java
@Configuration
public class AppConfig {

}
```

---

# Why Java Configuration?

Before Java configuration, Spring applications often had large XML files.

Problems:

- Difficult to maintain
- No compile-time checking
- Refactoring was harder
- IDE support was limited

Java configuration solved these issues by making configuration type-safe and easier to maintain.

---

# 3. What is `@Bean`?

`@Bean` tells Spring:

> "The object returned by this method should be registered as a Spring Bean."

Example:

```java
@Configuration
public class AppConfig {

    @Bean
    public PaymentService paymentService() {

        return new PaymentService();

    }

}
```

Startup flow:

```
Read Configuration Class

↓

Execute @Bean Method

↓

Receive Object

↓

Register Bean

↓

Manage Lifecycle
```

---

# Another Example

Suppose:

```java
public class PdfGenerator {

}
```

You cannot modify it.

Solution:

```java
@Configuration
public class PdfConfig {

    @Bean
    public PdfGenerator pdfGenerator() {

        return new PdfGenerator();

    }

}
```

Now Spring manages it just like any other bean.

---

# 4. `@Bean` vs `@Component`

This is one of the most common interview questions.

| Feature | `@Component` | `@Bean` |
|----------|--------------|----------|
| Applied On | Class | Method |
| Bean Creation | Component Scanning | Java Configuration |
| Third-party Classes | ❌ | ✅ |
| Automatic Discovery | Yes | No (`@Configuration` must be processed) |
| Typical Usage | Application classes | External libraries / Custom object creation |

---

# When Should You Use `@Component`?

If you own the source code.

Example:

```java
@Service
public class OrderService {

}
```

Spring discovers it automatically.

---

# When Should You Use `@Bean`?

If:

- The class comes from an external library.
- You need custom construction logic.
- You want to configure the object before Spring manages it.

Example:

```java
@Bean
public ObjectMapper objectMapper() {

    ObjectMapper mapper = new ObjectMapper();

    // Configure mapper

    return mapper;

}
```

---

# 5. Internal Startup Flow

Suppose Spring finds:

```java
@Configuration
public class AppConfig {

    @Bean
    public EmailService emailService() {

        return new EmailService();

    }

}
```

Spring performs the following conceptual steps:

```
Component Scan

↓

@Configuration Found

↓

Create Configuration Bean

↓

Find @Bean Methods

↓

Invoke Method

↓

Receive Object

↓

Create BeanDefinition

↓

Register Bean

↓

Bean Ready
```

Notice:

Spring treats objects returned from `@Bean` methods as first-class Spring beans.

---

# Internal Architecture

```
Application Startup

↓

Component Scanner

↓

@Configuration

↓

Configuration Processor

↓

Read @Bean Methods

↓

Invoke Methods

↓

Register Beans

↓

Dependency Injection

↓

Initialization

↓

Application Ready
```

---

# Real Project Example

Suppose your application uses:

- Redis client
- Kafka producer
- Jackson `ObjectMapper`
- HTTP client

These are often created using `@Bean` methods because they require custom configuration.

Example:

```java
@Configuration
public class InfrastructureConfig {

    @Bean
    public ObjectMapper objectMapper() {

        ObjectMapper mapper = new ObjectMapper();

        // Register modules, custom serializers, etc.

        return mapper;

    }

}
```

---

# Best Practices

✅ Use stereotype annotations (`@Service`, `@Repository`, etc.) for your own application classes.

✅ Use `@Bean` for third-party or infrastructure classes.

✅ Keep configuration classes focused on configuration.

✅ Avoid mixing business logic into configuration classes.

---

# Interview Perspective

## Question

Why do we need `@Bean` when `@Component` already exists?

Excellent Answer:

> `@Component` works only when you can annotate the class itself. For third-party classes or objects requiring custom construction logic, `@Bean` allows you to explicitly create and register the object as a Spring-managed bean.

---

## Question

What does `@Configuration` do?

Excellent Answer:

> `@Configuration` marks a class as a source of bean definitions. During startup, Spring processes these classes, executes their `@Bean` methods, and registers the returned objects as managed beans.

---

# Common Interview Traps

### Trap 1

Can `@Bean` be used outside a `@Configuration` class?

Technically, Spring can process `@Bean` methods in other managed classes under certain conditions, but **the recommended and interview-expected answer is to define them inside `@Configuration` classes**.

---

### Trap 2

Can I replace every `@Component` with `@Bean`?

Yes, but you shouldn't.

Component scanning is simpler and more maintainable for application classes.

---

### Trap 3

Does `@Bean` only work for third-party classes?

No.

It works for any object.

Third-party libraries are simply the most common use case.

---

# Summary

Remember:

- `@Configuration` defines Java-based Spring configuration.
- `@Bean` registers the object returned by a method as a Spring bean.
- Use `@Component` for your own classes.
- Use `@Bean` when you need explicit control over bean creation.
- Objects returned by `@Bean` methods participate in the full Spring bean lifecycle.

---

# Revision Cheat Sheet

```
Own Class?

↓

Yes

↓

@Component / @Service

--------------------

Third-Party Class?

↓

Yes

↓

@Configuration

↓

@Bean

↓

Spring Bean
```

---

# Exercises

1. Explain the difference between `@Bean` and `@Component`.
2. Why was Java Configuration introduced?
3. When would you choose `@Bean` over `@Component`?
4. Can a third-party class become a Spring bean?
5. Explain the startup flow for a `@Bean` method.

---

## End of Part 1

# Next Part (Most Important)

We'll cover one of the deepest Spring internals topics:

- How does `@Configuration` ensure singleton behavior?
- Why doesn't calling one `@Bean` method directly create multiple objects?
- What are **CGLIB proxies**?
- How does Spring intercept `@Bean` method calls?
- `proxyBeanMethods = true` vs `false`
- Full vs Lite Configuration

This is an advanced topic that appears in SDE-2 and senior backend interviews and explains one of Spring's most elegant internal implementations.