# Module 1 - Spring Fundamentals

# Chapter 08 - Bean Scopes

## Part 1 — Singleton & Prototype Scopes (The Most Important Bean Scopes)

---

# Interview Frequency

⭐⭐⭐⭐⭐ (Very High)

Asked In:

- Amazon
- Walmart
- Microsoft
- Goldman Sachs
- JP Morgan
- Visa
- PhonePe
- Razorpay
- Atlassian

---

# Goal

By the end of this chapter you should understand:

- What is Bean Scope?
- Why Bean Scopes exist
- Singleton Scope
- Prototype Scope
- Why Singleton is the default
- Memory implications
- Performance implications
- Thread safety concerns
- Interview scenarios

---

# Table of Contents

1. What is Bean Scope?
2. Why Do We Need Bean Scopes?
3. Singleton Scope
4. Prototype Scope
5. Singleton vs Prototype
6. Internal Working
7. Production Usage
8. Best Practices
9. Interview Questions
10. Cheat Sheet

---

# 1. What is Bean Scope?

## Interview Definition

A **Bean Scope** defines **how many instances of a Spring Bean are created** and **how long those instances live inside the Spring IoC Container**.

Think of it as a lifecycle policy.

It answers questions like:

- Should Spring create one object?
- One object per request?
- One object every time?
- One object per user session?

---

# Why Do Bean Scopes Exist?

Suppose we have:

```java
@Service
public class UserService {

}
```

Now imagine:

```
1000 HTTP Requests
```

Should Spring create

```
1000 UserService Objects?
```

or

```
1 UserService Object?
```

Different applications need different behaviors.

Bean Scope allows us to configure this.

---

# Real World Analogy

Imagine a hotel.

Some facilities are shared.

```
Lobby

↓

One

↓

Everyone Uses
```

Other facilities are private.

```
Hotel Room

↓

One Per Guest
```

Spring works similarly.

Some beans are shared.

Others are created individually.

---

# 2. Singleton Scope

Interview Frequency

⭐⭐⭐⭐⭐

Singleton is Spring's **default scope**.

Example:

```java
@Service
public class UserService {

}
```

No annotation required.

By default,

Spring creates exactly one instance.

---

# Visualization

```
Application Starts

↓

Create UserService

↓

Singleton Cache

↓

Request 1

↓

Same Object

---------------------

Request 2

↓

Same Object

---------------------

Request 3

↓

Same Object
```

Only one object exists.

---

# Internal Working

Spring maintains a singleton cache.

Conceptually:

```
Singleton Cache

↓

UserService

↓

Object Reference
```

Whenever another class requests:

```
UserService
```

Spring checks:

```
Singleton Cache

↓

Already Exists?

↓

Yes

↓

Return Existing Bean
```

No new object is created.

---

# Why Singleton?

Most enterprise services are:

- Stateless
- Lightweight
- Shared
- Reusable

Creating them repeatedly wastes memory.

---

# Memory Comparison

Without Singleton:

```
100 Requests

↓

100 UserService Objects
```

With Singleton:

```
100 Requests

↓

1 UserService Object
```

Huge memory savings.

---

# Performance Benefits

Creating objects is not free.

Every new object requires:

- Memory allocation
- Constructor execution
- Garbage collection later

Singleton avoids repeated object creation.

---

# Real Project Example

Imagine a fintech application.

```
PaymentController

↓

PaymentService

↓

PaymentRepository
```

Thousands of payment requests arrive.

Spring does **not** create thousands of services.

Instead:

```
One PaymentService

↓

Shared

↓

All Requests
```

---

# Thread Safety

Interviewers often ask:

> If there is only one PaymentService,

won't multiple requests modify it simultaneously?

Good question.

Answer:

It depends.

---

# Stateless Singleton

Safe.

Example:

```java
@Service
public class UserService {

    public User findById(Long id) {

        return repository.findById(id);

    }

}
```

No instance variables change.

Multiple threads can safely use this bean.

---

# Stateful Singleton

Dangerous.

Example:

```java
@Service
public class CounterService {

    private int counter;

}
```

Now two requests may modify

```
counter
```

simultaneously.

Race conditions occur.

---

# Best Practice

Singleton beans should almost always be

```
Stateless
```

Store request-specific data in:

- Local variables
- Method parameters
- Database
- Cache

Never in mutable instance fields.

---

# 3. Prototype Scope

Definition

Prototype means:

Create a **new bean every time it is requested**.

Example:

```java
@Component

@Scope("prototype")
public class ReportGenerator {

}
```

Now Spring behaves differently.

---

# Visualization

```
Need ReportGenerator

↓

Create Object A

-------------------

Need Again

↓

Create Object B

-------------------

Need Again

↓

Create Object C
```

Every request gets a new object.

---

# Internal Working

Unlike singleton,

Spring does **not** store prototype beans in the singleton cache.

Flow:

```
Need Bean

↓

Create New Object

↓

Return Object

↓

Forget Object
```

Notice something.

After returning the object,

Spring usually stops managing its lifecycle (except for initialization).

This becomes important for destruction callbacks.

---

# Singleton vs Prototype

| Feature | Singleton | Prototype |
|----------|-----------|-----------|
| Default Scope | ✅ | ❌ |
| Number of Objects | One | Many |
| Cached | Yes | No |
| Memory Usage | Low | Higher |
| Object Creation | Once | Every Request |
| Thread Sharing | Yes | No |

---

# Internal Comparison

## Singleton

```
Request

↓

Singleton Cache

↓

Object Exists

↓

Return Same Object
```

---

## Prototype

```
Request

↓

Create Object

↓

Return

↓

Discard Reference
```

---

# When Should You Use Prototype?

Good candidates:

- PDF generators
- Excel exporters
- Image processors
- Temporary builders
- Report generation
- Parsing utilities with internal mutable state

Bad candidates:

- Services
- Repositories
- Controllers
- Configuration classes

These should remain singleton.

---

# Interview Scenario

Question:

Suppose

```
Singleton Service

↓

Prototype Bean
```

Will Spring create a new prototype bean every method call?

Answer:

No.

Example:

```java
@Service
public class OrderService {

    @Autowired
    private ReportGenerator reportGenerator;

}
```

During startup:

```
Create Prototype

↓

Inject Once

↓

OrderService Ready
```

From then onwards,

the singleton keeps using the **same injected instance**.

This surprises many candidates.

To get a fresh prototype bean repeatedly, you need techniques such as `ObjectProvider`, `Provider`, or method injection (`@Lookup`), which we'll briefly discuss in advanced sections.

---

# Production Usage

Singleton

```
UserService

PaymentService

OrderService

Repository

Configuration

Security
```

Prototype

```
File Builder

PDF Generator

Excel Export

Parser

Report Builder
```

In real projects,

95% of beans are Singleton.

---

# Interview Perspective

## Question

Why is Singleton the default scope?

Excellent Answer:

> Most Spring-managed components are stateless services that can safely be shared across multiple requests. Reusing a single instance reduces object creation overhead, lowers memory usage, and improves application performance.

---

## Question

When should Prototype be used?

Excellent Answer:

> Prototype scope is suitable for stateful or short-lived objects where each caller should receive an independent instance, such as report generators or builder objects.

---

# Common Interview Traps

### Trap 1

Is Singleton the same as the GoF Singleton pattern?

❌ Not exactly.

GoF Singleton:

- One instance per JVM created by the class itself.

Spring Singleton:

- One instance **per Spring IoC Container** managed by the container.

---

### Trap 2

Does Prototype mean one object per HTTP request?

❌ No.

Prototype means:

A new object every time the bean is requested from the container.

HTTP Request scope is a different scope.

---

### Trap 3

Are Singleton beans automatically thread-safe?

❌ No.

Only **stateless** singleton beans are naturally thread-safe.

---

# Best Practices

✅ Keep Singleton beans stateless.

✅ Prefer Singleton unless there is a strong reason otherwise.

✅ Use Prototype only for mutable, short-lived objects.

✅ Don't store user-specific data inside singleton fields.

---

# Summary

Remember:

- Bean Scope controls how many bean instances Spring creates.
- Singleton is the default scope.
- Singleton beans are cached and reused.
- Prototype beans are created on demand.
- Singleton beans should be stateless.
- Prototype beans are useful for independent, mutable objects.

---

# Revision Cheat Sheet

```
Singleton

↓

One Object

↓

Cached

↓

Shared

-------------------------

Prototype

↓

New Object

↓

Every Request

↓

Not Cached
```

---

# Exercises

1. Explain Singleton and Prototype scopes.
2. Why is Singleton the default in Spring?
3. Are Singleton beans thread-safe?
4. Why doesn't injecting a Prototype bean into a Singleton automatically give you a new object each time?
5. Explain the difference between Spring Singleton and the GoF Singleton pattern.

---

## End of Part 1

# Next Part

We'll cover the remaining scopes and some of the most common interview scenarios:

- Request Scope
- Session Scope
- Application Scope
- WebSocket Scope
- Scoped Proxies
- Injecting Request-scoped beans into Singleton beans
- Thread safety interview scenarios
- Scope selection in real production applications