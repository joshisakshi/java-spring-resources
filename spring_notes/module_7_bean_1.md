# Module 1 - Spring Fundamentals

# Chapter 07 - Bean & Bean Lifecycle

## Part 1 — Understanding Beans and the Complete Lifecycle

---

# Interview Frequency

⭐⭐⭐⭐⭐ (Extremely High)

Asked In:

- Amazon
- Walmart
- Microsoft
- Atlassian
- Goldman Sachs
- JP Morgan
- Visa
- PhonePe
- Razorpay
- Almost every Spring Boot interview

---

# Goal

By the end of this chapter, you should understand:

- What is a Spring Bean?
- How is a Bean different from a normal Java object?
- Why do Beans exist?
- How does Spring create Beans?
- What is the complete Bean Lifecycle?
- Where does Dependency Injection fit into the lifecycle?
- Why is Bean Lifecycle important?

---

# Table of Contents

1. What is a Bean?
2. Bean vs Java Object
3. Why Beans Exist
4. Bean Lifecycle Overview
5. Complete Startup Flow
6. Internal Execution
7. Real Project Example
8. Interview Questions
9. Summary

---

# 1. What is a Bean?

## Interview Definition

A **Spring Bean** is an object that is created, configured, initialized, managed, and destroyed by the Spring IoC Container.

Notice the important part.

A bean is **not special because of its class.**

It is special because **Spring manages its lifecycle.**

---

# Java Object vs Spring Bean

Consider this class.

```java
public class UserService {

}
```

If you write:

```java
UserService service = new UserService();
```

This is **just a Java object**.

Spring knows nothing about it.

---

Now:

```java
@Service
public class UserService {

}
```

Spring discovers it during component scanning.

It becomes a **Spring Bean**.

The difference is not the class.

The difference is **who owns the object.**

---

# Comparison

| Java Object | Spring Bean |
|-------------|-------------|
| Created using `new` | Created by Spring |
| You manage lifecycle | Spring manages lifecycle |
| No Dependency Injection | Supports Dependency Injection |
| No lifecycle callbacks | Supports lifecycle callbacks |
| No AOP | Supports AOP |
| No transactions | Supports transactions |

---

# Interview Trick

Question:

Is every Java object a Spring Bean?

Answer:

No.

Only objects managed by the Spring IoC Container are Spring Beans.

---

# 2. Why Do Beans Exist?

Imagine a project with:

```
Controllers

25

Services

80

Repositories

40

Configurations

15
```

That's more than 150 objects.

Questions:

Who creates them?

Who injects dependencies?

Who destroys them?

Who ensures only one singleton exists?

Without Spring,

you would manage all of this yourself.

With Spring,

the container manages everything.

---

# Bean Management

```
Bean

↓

Created

↓

Configured

↓

Dependencies Injected

↓

Initialized

↓

Used

↓

Destroyed
```

Notice:

Spring manages the **entire lifecycle**.

---

# 3. Bean Lifecycle Overview

This is one of the most important diagrams in Spring.

```
Component Scan

↓

Create BeanDefinition

↓

Instantiate Bean

↓

Inject Dependencies

↓

Aware Interfaces

↓

BeanPostProcessor (Before)

↓

@PostConstruct

↓

afterPropertiesSet()

↓

Custom init()

↓

BeanPostProcessor (After)

↓

Bean Ready

↓

Application Running

↓

@PreDestroy

↓

destroy()

↓

Custom destroy()
```

Almost every lifecycle interview question comes from this diagram.

---

# High-Level Lifecycle

Let's simplify it first.

```
Application Starts

↓

Find Bean

↓

Create Bean

↓

Inject Dependencies

↓

Initialize Bean

↓

Bean Ready

↓

Application Stops

↓

Destroy Bean
```

This is the mental model you should always have.

---

# 4. Complete Startup Flow

Suppose we have:

```java
@Service
public class UserService {

}
```

Spring startup looks like this.

```
Application Starts

↓

Component Scan

↓

Find UserService

↓

Create BeanDefinition

↓

Register BeanDefinition

↓

Instantiate UserService

↓

Inject Dependencies

↓

Initialize Bean

↓

Store Singleton

↓

Application Ready
```

Notice that **BeanDefinition** comes before object creation.

This reinforces what we learned in previous chapters.

---

# Where Does Dependency Injection Happen?

A common interview question.

The answer:

```
Instantiate Bean

↓

Inject Dependencies

↓

Initialize Bean
```

Dependency Injection happens **after the object is created but before initialization callbacks**.

This ordering matters.

---

# Internal Execution

Conceptually,

Spring performs something similar to:

```
Need Bean

↓

Read BeanDefinition

↓

Call Constructor

↓

Create Object

↓

Resolve Dependencies

↓

Inject Dependencies

↓

Initialize Bean

↓

Return Managed Bean
```

This is repeated for every bean in the application.

---

# Bean Lifecycle Is Not Just Creation

Many candidates think:

```
Create Bean

↓

Done
```

Wrong.

The bean remains under Spring's management.

Spring may:

- Wrap it in a proxy
- Apply AOP
- Apply transactions
- Inject additional dependencies
- Invoke destruction callbacks
- Monitor lifecycle events

This is why the lifecycle is much more than object creation.

---

# Real Project Example

Suppose you're building a payment application.

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

Initialize Repository

↓

Create FraudDetectionService

↓

Inject Repository

↓

Initialize

↓

Create PaymentService

↓

Inject Dependencies

↓

Initialize

↓

Create Controller

↓

Inject PaymentService

↓

Application Ready
```

By the time the first request arrives,

every required bean has already completed its lifecycle up to the "ready" state.

---

# Interview Perspective

⭐⭐⭐⭐⭐

## Question

What is a Spring Bean?

### Weak Answer

A bean is an object created by Spring.

---

### Good Answer

A bean is an object managed by the Spring IoC Container.

---

### Excellent Answer

A Spring Bean is an object whose complete lifecycle—including creation, dependency injection, initialization, and destruction—is managed by the Spring IoC Container. Because Spring controls the bean, it can also provide additional features such as AOP, transactions, lifecycle callbacks, and dependency injection.

---

# Common Interview Traps

### Trap 1

Does `@Service` create an object?

❌ No.

It marks the class as a candidate for bean registration.

The container creates the object later.

---

### Trap 2

Is every singleton a Spring Bean?

❌ No.

A singleton created with plain Java is still not a Spring Bean.

Spring management is what matters.

---

### Trap 3

When does Dependency Injection occur?

✅ After bean instantiation.

✅ Before initialization callbacks.

---

# Best Practices

✅ Let Spring manage application services.

✅ Avoid manually instantiating Spring-managed components.

✅ Keep business logic inside managed beans.

✅ Understand lifecycle callbacks before using them.

---

# Summary

Remember:

- A Spring Bean is a managed object.
- Bean management is more important than object creation.
- The lifecycle consists of creation, injection, initialization, usage, and destruction.
- Dependency Injection is only one step within the complete lifecycle.
- Spring can only apply advanced features to managed beans.

---

# Revision Cheat Sheet

```
BeanDefinition

↓

Instantiate Bean

↓

Dependency Injection

↓

Initialize Bean

↓

Ready

↓

Destroy Bean
```

---

# Exercises

1. Explain the difference between a Java object and a Spring Bean.
2. Where does Dependency Injection fit in the Bean Lifecycle?
3. Why is a Bean different from an object created using `new`?
4. Why does Spring manage the complete lifecycle instead of only object creation?

---

## End of Part 1

# Next Part (Most Asked Lifecycle Topic)

We'll go through the **actual Bean Lifecycle callbacks** one by one:

- `@PostConstruct`
- `@PreDestroy`
- `InitializingBean`
- `DisposableBean`
- Custom `initMethod`
- Custom `destroyMethod`
- `BeanPostProcessor`
- Complete execution order
- Which callback runs first?
- Which one should you use in production?
- Common interview traps

> ⭐⭐⭐⭐⭐ This is one of the highest-yield Spring interview topics because it combines annotations, lifecycle, and framework internals into one discussion.