# Module 1 - Spring Fundamentals

# 01 - What is Spring?

> Part 1

---

# Goal

After completing this chapter, you should be able to answer the following interview questions confidently:

- What is Spring?
- Why was Spring introduced?
- What problems existed before Spring?
- Why did Java Enterprise development become difficult?
- Why is Spring called a lightweight framework?
- How does Spring simplify enterprise application development?
- Why do almost all modern Java backend projects use Spring or Spring Boot?

This chapter builds the foundation for everything else in Spring Boot. If you understand **why Spring exists**, concepts like IoC, Dependency Injection, Beans, ApplicationContext, Auto Configuration, AOP, and Spring Boot become much easier.

---

# Table of Contents

1. Introduction
2. Why Spring Was Introduced
3. Problems Before Spring
4. What is Spring?
5. Why is Spring Called a Framework?
6. Real-World Analogy
7. Evolution of Enterprise Java
8. Why Companies Adopted Spring
9. Spring Ecosystem Overview
10. First Spring Example
11. Interview Notes

---

# 1. Intuition

Before learning Spring, imagine you are building a large shopping website.

It has:

- User Management
- Authentication
- Product Service
- Payment Service
- Order Service
- Email Service
- Inventory Service

Every service depends on many other classes.

Example:

```
OrderService

needs

↓

PaymentService

↓

InventoryService

↓

EmailService

↓

NotificationService
```

Without Spring, you would manually create every object.

```java
PaymentService paymentService = new PaymentService();

InventoryService inventoryService = new InventoryService();

EmailService emailService = new EmailService();

OrderService orderService =
        new OrderService(
                paymentService,
                inventoryService,
                emailService
        );
```

Looks manageable?

Now imagine:

- 300 classes
- 800 dependencies
- 50 developers
- Multiple environments
- Unit testing
- Database connections
- Security configuration
- Caching
- Logging

Manually managing object creation quickly becomes difficult.

Spring was created to solve this problem.

---

# Why Was This a Problem?

Large enterprise applications had several recurring issues.

## Problem 1 — Manual Object Creation

Every object had to be created manually.

```
UserService

↓

new UserRepository()

↓

new DatabaseConnection()

↓

new Driver()

↓

Configuration
```

Developers spent significant time wiring objects together instead of writing business logic.

---

## Problem 2 — Tight Coupling

Suppose your service depends on a MySQL repository.

```java
UserRepository repository = new MySQLRepository();
```

Later, your company decides to migrate to PostgreSQL.

Now every place using `MySQLRepository` must change.

Your business logic becomes tightly coupled to specific implementations.

---

## Problem 3 — Difficult Testing

Suppose you want to test:

```java
OrderService
```

But it internally creates:

```java
new PaymentService()

new EmailService()

new InventoryService()

new DatabaseConnection()
```

Now your unit test accidentally connects to:

- Database
- Email server
- Payment gateway

This is not a true unit test.

Spring later solves this through Dependency Injection, allowing you to replace real dependencies with mocks.

---

## Problem 4 — Configuration Everywhere

Older Java EE applications required configuration files like:

```
web.xml

application.xml

ejb.xml

server.xml

hibernate.cfg.xml

beans.xml
```

Projects often contained thousands of lines of XML.

Maintaining these files was tedious and error-prone.

---

## Problem 5 — Boilerplate Code

Developers repeatedly wrote code for:

- Transactions
- Logging
- Security
- Database connection management
- Object creation
- Exception handling

The actual business logic often made up only a small portion of the codebase.

---

# Real-World Analogy

Imagine you're opening a restaurant.

Without Spring, the head chef must:

- Hire employees
- Purchase ingredients
- Build the kitchen
- Install electricity
- Arrange tables
- Buy utensils
- Clean the restaurant
- Cook food

Most of the chef's time is spent on setup rather than cooking.

With Spring, it's like walking into a fully equipped kitchen.

Everything is already prepared:

- Kitchen
- Staff
- Ingredients
- Utilities
- Equipment

The chef focuses only on cooking.

Similarly, Spring manages infrastructure so developers can focus on business logic.

---

# 2. Definition

### Beginner Definition

Spring is a Java framework that simplifies the development of enterprise applications by managing object creation, dependencies, configuration, and common infrastructure concerns.

---

### Interview Definition

> Spring is a lightweight, open-source, modular Java framework that implements Inversion of Control (IoC) and Dependency Injection (DI) to build loosely coupled, maintainable, testable, and scalable enterprise applications.

---

Let's break that definition down.

| Term | Meaning |
|------|---------|
| Lightweight | You use only the modules you need. |
| Open Source | Freely available and community-driven. |
| Modular | Consists of independent modules like Core, MVC, Security, Data, etc. |
| IoC | Spring controls object creation instead of the developer. |
| Dependency Injection | Spring injects required dependencies automatically. |
| Enterprise | Suitable for large-scale production systems. |

---

# Why Is Spring Called a Framework?

A library is something **you call**.

A framework is something that **calls your code**.

### Library

```
Your Code

↓

Library Function
```

You remain in control.

---

### Framework

```
Framework Starts

↓

Framework Creates Objects

↓

Framework Calls Your Code

↓

Framework Manages Lifecycle
```

This concept is known as **Inversion of Control**, which we'll study in depth later.

---

# Spring's Core Philosophy

Spring aims to let developers focus on solving business problems instead of infrastructure problems.

Instead of worrying about:

- Object creation
- Lifecycle management
- Configuration
- Dependency wiring

Developers write business logic like:

```java
public void placeOrder() {
    // Business logic only
}
```

Spring takes care of the surrounding infrastructure.

---

# First Look at Spring

Without Spring:

```java
UserRepository repository = new UserRepository();

UserService service = new UserService(repository);
```

With Spring:

```java
@Service
public class UserService {

    private final UserRepository repository;

    public UserService(UserRepository repository) {
        this.repository = repository;
    }
}
```

Notice that there is **no `new UserRepository()`**.

Spring creates the object and injects it automatically.

We'll explore how this works internally in later chapters.

---

# Spring at a High Level

```
                Your Application
                       │
                       ▼
        ┌────────────────────────┐
        │      Spring Framework  │
        ├────────────────────────┤
        │ IoC Container          │
        │ Dependency Injection   │
        │ Spring MVC             │
        │ Spring Data            │
        │ Spring Security        │
        │ Spring AOP             │
        │ Transactions           │
        └────────────────────────┘
                       │
                       ▼
                  Database
```

---

# Interview Notes

### Remember These Points

- Spring is **not** an application server.
- Spring is **not** a programming language.
- Spring is **not** a replacement for Java.
- Spring is a framework built on top of Java.
- Spring manages objects through an IoC Container.
- Spring encourages loose coupling and high cohesion.
- Spring reduces boilerplate code.
- Spring makes applications easier to test and maintain.

---

## End of Part 1

**Next Part (Part 2) will cover:**

- History of Spring
- Rod Johnson and the birth of Spring
- Why EJB failed
- Spring Architecture Overview
- Spring Modules
- Internal architecture
- How Spring fits into modern backend applications
- Production usage in large companies
- More interview questions and diagrams