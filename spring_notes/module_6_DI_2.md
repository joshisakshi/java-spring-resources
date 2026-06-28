# Module 1 - Spring Fundamentals

# Chapter 06 - Dependency Injection (DI)

## Part 2 — `@Autowired` & Dependency Resolution Internals

---

# Interview Frequency

⭐⭐⭐⭐⭐ (Extremely High)

Asked In:

- Amazon
- Microsoft
- Walmart Global Tech
- Atlassian
- Goldman Sachs
- JP Morgan
- Visa
- PhonePe
- Razorpay
- Almost every Spring interview

---

# Goal

By the end of this chapter, you will understand:

- What `@Autowired` actually does
- How Spring resolves dependencies
- What happens internally when Spring sees `@Autowired`
- How Spring chooses which bean to inject
- Why dependency resolution sometimes fails
- Why constructor injection is preferred

---

# Table of Contents

1. What is `@Autowired`?
2. Where Can It Be Used?
3. Internal Working of Dependency Injection
4. Dependency Resolution Algorithm
5. Constructor Injection
6. Field Injection
7. Setter Injection
8. Common Errors
9. Interview Questions
10. Cheat Sheet

---

# 1. What is `@Autowired`?

## Interview Definition

`@Autowired` is Spring's annotation used to request that the IoC container inject a suitable bean into a dependent object.

Notice something important.

`@Autowired` **does not create objects.**

It only tells Spring:

> "This class needs a dependency."

Spring then tries to find a matching bean.

---

# Common Misconception

Many beginners think:

```
@Autowired

↓

Creates Object
```

Wrong.

The actual flow is:

```
IoC Container

↓

Creates Bean

↓

@Autowired

↓

Inject Existing Bean
```

Object creation and dependency injection are two different phases.

---

# Example

```java
@Service
public class UserService {

    @Autowired
    private EmailService emailService;

}
```

Spring does **not** create EmailService here.

Instead:

```
Application Startup

↓

Create EmailService Bean

↓

Create UserService Bean

↓

Inject EmailService

↓

Application Ready
```

---

# 2. Where Can `@Autowired` Be Used?

Spring supports three injection styles.

### Constructor Injection

```java
@Service
public class UserService {

    private final EmailService emailService;

    @Autowired
    public UserService(EmailService emailService) {
        this.emailService = emailService;
    }
}
```

---

### Field Injection

```java
@Service
public class UserService {

    @Autowired
    private EmailService emailService;

}
```

---

### Setter Injection

```java
@Service
public class UserService {

    private EmailService emailService;

    @Autowired
    public void setEmailService(EmailService emailService) {
        this.emailService = emailService;
    }
}
```

We'll compare all three shortly.

---

# 3. Internal Working of Dependency Injection

Let's look at what happens when Spring starts.

Suppose we have:

```java
@Service
public class UserService {

    private final EmailService emailService;

    public UserService(EmailService emailService) {
        this.emailService = emailService;
    }
}
```

And:

```java
@Service
public class EmailService {

}
```

---

# High-Level Flow

```
Application Starts

↓

Component Scan

↓

Create BeanDefinitions

↓

Register BeanDefinitions

↓

Create EmailService Bean

↓

Create UserService Bean

↓

Resolve Constructor Parameter

↓

Inject EmailService

↓

Store UserService Bean

↓

Application Ready
```

Notice that Spring creates `EmailService` first because `UserService` depends on it.

---

# Internal Execution (Spring Perspective)

Conceptually, Spring performs:

```
Need UserService Bean

↓

Inspect Constructor

↓

Find Parameter

↓

EmailService

↓

Search Bean Registry

↓

Matching Bean Found

↓

Inject Bean

↓

Finish UserService Creation
```

This dependency resolution happens automatically.

---

# 4. Dependency Resolution Algorithm

When Spring needs a dependency, it follows a predictable process.

### Step 1

Determine the required type.

Example:

```java
EmailService
```

---

### Step 2

Search all registered beans.

```
Bean Registry

↓

UserService

EmailService

OrderService

PaymentService
```

---

### Step 3

Find matching bean by type.

```
Required

↓

EmailService

↓

Found

↓

EmailService Bean
```

---

### Step 4

Inject dependency.

```
Constructor

↓

EmailService Injected

↓

UserService Ready
```

---

# What If No Bean Exists?

Suppose:

```java
@Autowired
private EmailService emailService;
```

But Spring never created an EmailService bean.

Startup fails.

Typical error:

```text
No qualifying bean of type
'com.example.EmailService'
available
```

This is one of the most common Spring errors.

---

# What If Multiple Beans Exist?

Suppose:

```java
@Service
class GmailService {}

@Service
class OutlookService {}
```

Both implement:

```java
EmailService
```

Now Spring sees:

```
Need EmailService

↓

Found

GmailService

OutlookService
```

Which one should it inject?

It doesn't know.

Application startup fails with:

```text
NoUniqueBeanDefinitionException
```

We'll solve this later using:

- `@Qualifier`
- `@Primary`

---

# 5. Constructor Injection

This is Spring's recommended approach.

Example:

```java
@Service
public class OrderService {

    private final PaymentService paymentService;

    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```

---

# Why Constructor Injection Is Preferred

## Dependencies are mandatory

The object cannot exist without its required dependencies.

---

## Supports immutability

Notice:

```java
private final PaymentService paymentService;
```

The dependency cannot change after construction.

---

## Easy to test

Testing becomes straightforward.

```java
PaymentService mock = new MockPaymentService();

OrderService service =
        new OrderService(mock);
```

No Spring container required.

---

## Detects problems early

Missing dependencies are detected during bean creation.

---

# 6. Field Injection

Example:

```java
@Autowired
private PaymentService paymentService;
```

Looks shorter.

But has disadvantages.

---

# Problems with Field Injection

## Hidden Dependencies

Looking only at the constructor,

you don't know what the class requires.

---

## Difficult Unit Testing

You cannot simply write:

```java
new UserService(...)
```

because there is no constructor.

Testing usually requires reflection or Spring support.

---

## Violates Immutability

The field cannot be declared `final`.

---

# 7. Setter Injection

Useful when dependencies are optional.

Example:

```java
@Autowired
public void setLogger(Logger logger) {

    this.logger = logger;

}
```

Common use cases:

- Optional dependencies
- Configuration objects
- Legacy applications

For required business dependencies, constructor injection is preferred.

---

# Constructor vs Field vs Setter

| Feature | Constructor | Field | Setter |
|---------|------------|--------|--------|
| Recommended | ✅ | ❌ | Sometimes |
| Immutable | ✅ | ❌ | ❌ |
| Easy Testing | ✅ | ❌ | ✅ |
| Mandatory Dependencies | ✅ | ❌ | ❌ |
| Optional Dependencies | ❌ | ❌ | ✅ |

---

# Internal Execution (Source Code Perspective)

Don't memorize these classes.

Just understand the flow.

```
ApplicationContext

↓

DefaultListableBeanFactory

↓

Create Bean

↓

AutowiredAnnotationBeanPostProcessor

↓

Inspect Injection Points

↓

Resolve Dependencies

↓

Inject Bean

↓

Initialize Bean
```

Notice:

`@Autowired` is processed by a **BeanPostProcessor**, not by the annotation itself.

This is a common interview discussion point for experienced candidates.

---

# Interview Perspective

⭐⭐⭐⭐⭐

## Question

How does `@Autowired` work internally?

### Weak Answer

Spring injects the object.

---

### Good Answer

Spring searches for a bean of the required type and injects it into the dependent class.

---

### Excellent Answer

During bean creation, Spring's dependency resolution mechanism inspects constructors, fields, or setter methods marked for injection. It resolves matching beans from the IoC container and injects them before the bean completes initialization. The processing of `@Autowired` is handled by Spring infrastructure such as `AutowiredAnnotationBeanPostProcessor`.

---

# Common Interview Traps

### Trap 1

**Question**

Does `@Autowired` create beans?

**Answer**

No.

The IoC container creates beans.

`@Autowired` only requests injection of an already managed bean.

---

### Trap 2

**Question**

Can `@Autowired` work on any object?

**Answer**

No.

Spring only injects dependencies into Spring-managed beans.

If you create an object using:

```java
new UserService();
```

Spring is not involved.

No injection occurs.

---

### Trap 3

**Question**

Which injection style is recommended?

**Answer**

Constructor Injection.

It promotes immutability, explicit dependencies, and easier testing.

---

# Real Project Example

Consider a payment service.

```
PaymentController

↓

PaymentService

↓

FraudDetectionService

↓

PaymentRepository
```

During startup:

```
Create Repository

↓

Create FraudDetectionService

↓

Create PaymentService

↓

Inject Repository

↓

Inject FraudDetectionService

↓

Create Controller

↓

Inject PaymentService

↓

Application Ready
```

By the time the first HTTP request arrives, every required dependency has already been resolved.

---

# Summary

Key takeaways:

- `@Autowired` requests dependency injection.
- It does not create beans.
- Spring resolves dependencies by type.
- Constructor injection is the recommended approach.
- Multiple matching beans require additional configuration (`@Qualifier`, `@Primary`).
- Injection happens during bean creation, before initialization completes.

---

# Revision Cheat Sheet

```
Need Dependency

↓

Inspect Constructor / Field / Setter

↓

Search Bean Registry

↓

Find Matching Bean

↓

Inject Dependency

↓

Bean Ready
```

---

# Exercises

1. Explain how Spring resolves dependencies.
2. Why is constructor injection preferred?
3. What happens if no matching bean exists?
4. What happens if two beans of the same type exist?
5. Why doesn't `@Autowired` work on objects created using `new`?

---

## End of Part 2

# Next Part (One of the Best Chapters)

We'll go even deeper into Spring internals and answer:

- What is `@Qualifier`?
- What is `@Primary`?
- How does Spring choose one bean among many?
- What is bean ambiguity?
- How are constructor parameters matched?
- What are circular dependencies?
- Why does Spring fail with circular constructor injection?
- How does Spring resolve some circular dependencies but not others?

> ⭐⭐⭐⭐⭐ This chapter contains some of the most commonly asked scenario-based Spring interview questions.