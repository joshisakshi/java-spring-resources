# Module 1 - Spring Fundamentals

# Chapter 05 - Inversion of Control (IoC)

## Part 2 — IoC Container Internals

---

# Interview Frequency

⭐⭐⭐⭐⭐

Frequently Asked In:

- Amazon
- Walmart
- Goldman Sachs
- JP Morgan
- Visa
- Atlassian
- Microsoft
- Product-based Companies

---

# Goal

In Part 1, we understood **what IoC is**.

In this chapter, we'll answer:

- What exactly is an IoC Container?
- Is BeanFactory an IoC Container?
- Is ApplicationContext an IoC Container?
- What does the container actually do?
- How does Spring know which objects to create?
- What happens during application startup?

---

# Table of Contents

1. What is an IoC Container?
2. Responsibilities of the IoC Container
3. BeanFactory
4. ApplicationContext
5. BeanFactory vs ApplicationContext
6. Complete Bean Creation Flow
7. Internal Architecture
8. Interview Questions
9. Summary

---

# 1. What is an IoC Container?

## Definition

An **IoC Container** is the core component of Spring responsible for:

- Creating beans
- Configuring beans
- Injecting dependencies
- Managing bean lifecycle
- Destroying beans when the application shuts down

Think of it as a **bean manager**.

It owns every Spring-managed object.

---

# Real World Analogy

Imagine a hotel.

You don't hire:

- Receptionist
- Housekeeping
- Security
- Chef

individually.

The hotel management takes care of everything.

Similarly,

you don't manually create:

```
UserService

PaymentService

OrderRepository

NotificationService
```

The IoC Container manages them.

---

# Container Responsibilities

The IoC Container performs much more than object creation.

```
                IoC Container

                      │

      ┌───────────────┼────────────────┐

      ▼               ▼                ▼

Create Beans    Inject Dependencies   Manage Lifecycle

      │

      ▼

Destroy Beans
```

Let's understand each responsibility.

---

## Responsibility 1 — Create Beans

Suppose Spring finds:

```java
@Service
public class UserService {

}
```

The container creates an instance.

```
UserService.class

↓

Constructor

↓

Object Created
```

---

## Responsibility 2 — Inject Dependencies

Suppose:

```java
@Service
public class UserService {

    private final EmailService emailService;

    public UserService(EmailService emailService) {

        this.emailService = emailService;

    }

}
```

The container first creates:

```
EmailService
```

Then:

```
UserService

↓

Constructor Injection

↓

Ready
```

---

## Responsibility 3 — Manage Lifecycle

The container knows:

```
Create Bean

↓

Initialize Bean

↓

Keep Alive

↓

Destroy Bean
```

You don't write lifecycle management yourself.

---

## Responsibility 4 — Maintain Singleton Objects

Suppose three controllers need:

```
UserService
```

Without Spring:

```
new UserService()

new UserService()

new UserService()
```

Three different objects.

With Spring (default singleton scope):

```
UserService

↓

One Object

↓

Shared Everywhere
```

This improves memory usage and consistency.

---

# 2. BeanFactory

Interview Frequency:

⭐⭐⭐⭐⭐

BeanFactory is Spring's **basic IoC Container**.

It defines the minimum functionality required to manage beans.

Simplified view:

```java
public interface BeanFactory {

    Object getBean(String name);

}
```

Its primary job is:

```
Need Bean

↓

getBean()

↓

Return Managed Object
```

---

# Lazy Initialization

One important characteristic of BeanFactory:

Beans are generally created **only when requested**.

Example:

```
Application Starts

↓

BeanFactory Ready

↓

User asks for Bean

↓

Bean Created
```

This is called **lazy initialization**.

---

# Why Is This Useful?

Imagine:

```
1000 Beans
```

But only:

```
100
```

are ever used.

BeanFactory avoids creating unnecessary objects.

---

# 3. ApplicationContext

Interview Frequency:

⭐⭐⭐⭐⭐

Almost every Spring Boot application uses an ApplicationContext.

Technically,

ApplicationContext **extends BeanFactory**.

```
BeanFactory

       ▲

       │

ApplicationContext
```

So ApplicationContext can do everything BeanFactory can.

Plus much more.

---

# Additional Features

ApplicationContext adds:

- Component Scanning
- Event Publishing
- Internationalization (i18n)
- Environment Support
- Resource Loading
- Automatic BeanPostProcessor Registration

This is why Spring Boot uses ApplicationContext.

---

# Eager Initialization

Unlike BeanFactory,

ApplicationContext usually creates singleton beans during startup.

```
Application Starts

↓

Scan Components

↓

Create Beans

↓

Application Ready
```

Advantages:

- Startup errors are detected immediately.
- Faster request processing later.
- All dependencies are validated during startup.

---

# BeanFactory vs ApplicationContext

| Feature | BeanFactory | ApplicationContext |
|----------|-------------|--------------------|
| IoC Container | ✅ | ✅ |
| Dependency Injection | ✅ | ✅ |
| Bean Lifecycle | ✅ | ✅ |
| Lazy Initialization | ✅ Default | ❌ (Singletons eagerly created by default) |
| Event Publishing | ❌ | ✅ |
| Component Scanning | Limited support | ✅ |
| Environment Support | ❌ | ✅ |
| Enterprise Features | ❌ | ✅ |

---

# Which One Should You Use?

Interview Answer:

Use **ApplicationContext**.

BeanFactory is the foundational interface.

ApplicationContext is its richer, enterprise-ready implementation.

In Spring Boot, you almost never interact directly with BeanFactory.

---

# 4. Complete Startup Flow

Let's combine everything we've learned.

Suppose you run:

```java
SpringApplication.run(App.class, args);
```

Conceptually:

```
Application Starts

↓

Create ApplicationContext

↓

Component Scan

↓

Find @Component

↓

Create BeanDefinitions

↓

Register BeanDefinitions

↓

Instantiate Singleton Beans

↓

Inject Dependencies

↓

Run Initialization Callbacks

↓

Application Ready
```

This is one of the most important diagrams in Spring.

---

# Internal Architecture

```
                  Spring Boot

                       │

                       ▼

             ApplicationContext

                       │

              extends BeanFactory

                       │

                       ▼

            BeanDefinition Registry

                       │

                       ▼

              Bean Definitions

                       │

                       ▼

             Bean Instantiation

                       │

                       ▼

           Dependency Injection

                       │

                       ▼

              Singleton Cache

                       │

                       ▼

            Ready to Serve Requests
```

Notice the order.

Spring never starts with object creation.

It starts with **metadata**.

---

# Interview Perspective

⭐⭐⭐⭐⭐

### Question

What is the IoC Container?

### Weak Answer

"It creates beans."

---

### Good Answer

"It creates and manages Spring beans."

---

### Excellent Answer

"The IoC Container is responsible for creating, configuring, injecting, initializing, and destroying Spring-managed beans. In Spring Boot, the ApplicationContext serves as the primary IoC container, coordinating the complete lifecycle of application beans."

---

# Common Interview Traps

---

### Trap 1

**Question**

Is BeanFactory obsolete?

**Correct Answer**

No.

BeanFactory is the foundation of Spring's IoC architecture.

ApplicationContext builds on top of it.

---

### Trap 2

**Question**

Which IoC container does Spring Boot use?

**Correct Answer**

ApplicationContext.

---

### Trap 3

**Question**

Who creates objects in Spring?

**Correct Answer**

The IoC Container creates Spring-managed beans.

---

### Trap 4

**Question**

Does ApplicationContext replace BeanFactory?

**Correct Answer**

No.

It extends BeanFactory and provides additional enterprise capabilities.

---

# Real Project Example

Imagine you're building a payment system.

Classes:

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
ApplicationContext

↓

Scans all packages

↓

Creates BeanDefinitions

↓

Creates Repository

↓

Creates FraudDetectionService

↓

Creates PaymentService

↓

Creates Controller

↓

Injects Dependencies

↓

Application Ready
```

When the first payment request arrives,

all required beans already exist.

No additional object creation is needed.

---

# Summary

The IoC Container is responsible for the complete lifecycle of Spring beans.

Remember:

- BeanFactory is the basic IoC container.
- ApplicationContext extends BeanFactory.
- Spring Boot uses ApplicationContext.
- The container creates, injects, initializes, and destroys beans.
- BeanDefinitions are processed before actual bean instantiation.

---

# Revision Cheat Sheet

```
IoC Container

↓

ApplicationContext

↓

BeanFactory

↓

BeanDefinition

↓

Bean Creation

↓

Dependency Injection

↓

Initialization

↓

Singleton Cache

↓

Application Ready
```

---

# Exercises

## Conceptual

1. Explain the responsibilities of the IoC Container.
2. Compare BeanFactory and ApplicationContext.
3. Why does Spring Boot prefer ApplicationContext?

## Interview Practice

**Q:** What is the difference between BeanFactory and ApplicationContext?

Try answering in under 90 seconds.

A strong answer should cover:
- Relationship (inheritance)
- Lazy vs eager initialization
- Additional enterprise features
- Which one is used in Spring Boot

---

## End of Part 2

### Next Part

We'll answer the question that almost every interviewer eventually asks:

> **"How does Spring actually perform Dependency Injection internally?"**

Before we learn `@Autowired`, we need to understand:

- How Spring resolves dependencies
- How it decides which bean to inject
- Constructor vs Field vs Setter injection
- Circular dependencies
- Bean resolution algorithm

This will naturally lead us into the next chapter: **Dependency Injection**.