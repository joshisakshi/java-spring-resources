# Module 1 - Spring Fundamentals

# Chapter 11 - Spring Container, BeanFactory & ApplicationContext

---

# Interview Frequency

⭐⭐⭐⭐⭐ (Very High)

Asked In:

- Amazon
- Microsoft
- Walmart
- Goldman Sachs
- JP Morgan
- Atlassian
- Oracle
- Almost every Spring interview

Common Questions:

- What is the Spring Container?
- What is BeanFactory?
- What is ApplicationContext?
- Difference between BeanFactory and ApplicationContext
- Which one does Spring Boot use?
- Why was ApplicationContext introduced?

---

# Goal

By the end of this chapter you should understand:

- What is the Spring Container?
- BeanFactory
- ApplicationContext
- Internal hierarchy
- Bean loading strategies
- Startup flow
- Production usage
- Interview differences

---

# Table of Contents

1. What is the Spring Container?
2. BeanFactory
3. ApplicationContext
4. BeanFactory vs ApplicationContext
5. Internal Architecture
6. Startup Flow
7. Production Usage
8. Best Practices
9. Interview Questions

---

# 1. What is the Spring Container?

## Interview Definition

The **Spring Container** is the core component of the Spring Framework responsible for:

- Creating beans
- Managing bean lifecycle
- Performing Dependency Injection
- Maintaining bean scopes
- Managing configuration metadata

You can think of the Spring Container as the **runtime environment** that manages all Spring beans.

Without the container:

```
No IoC

No DI

No Bean Lifecycle

No AOP

No Transactions

No Spring
```

---

# Responsibilities of the Spring Container

The container performs many tasks during startup.

```
Application Starts

↓

Read Configuration

↓

Component Scan

↓

Create BeanDefinitions

↓

Instantiate Beans

↓

Inject Dependencies

↓

Initialize Beans

↓

Store Singleton Beans

↓

Application Ready
```

Everything we learned so far happens inside the container.

---

# 2. BeanFactory

## Definition

`BeanFactory` is the **most basic IoC container** provided by Spring.

It is responsible for:

- Creating beans
- Dependency Injection
- Bean lifecycle management

Example:

```java
BeanFactory factory = ...;
UserService service = factory.getBean(UserService.class);
```

---

# Lazy Initialization

One important characteristic of `BeanFactory`:

Beans are typically created **only when requested**.

Conceptually:

```
Application Starts

↓

No Bean Created Yet

↓

getBean(UserService)

↓

Create Bean

↓

Return Bean
```

This is called **lazy initialization**.

---

# Advantages

- Lower startup time
- Lower initial memory usage

---

# Limitations

BeanFactory provides only the basic IoC features.

It does **not** automatically provide many enterprise conveniences such as:

- Event publishing
- Easy internationalization (i18n)
- Convenient resource loading
- Automatic registration of many infrastructure components

These capabilities are added by `ApplicationContext`.

---

# 3. ApplicationContext

## Definition

`ApplicationContext` is the **advanced Spring container**.

It extends `BeanFactory`.

```
BeanFactory

↓

ApplicationContext
```

Therefore,

everything BeanFactory can do,

ApplicationContext can do.

Plus much more.

---

# Additional Features

ApplicationContext provides:

✅ Dependency Injection

✅ Bean Lifecycle

✅ BeanPostProcessors

✅ Event Publishing

✅ Message Source (i18n)

✅ Resource Loading

✅ Environment & Profiles

✅ Automatic infrastructure integration

---

# Startup Behavior

Unlike BeanFactory,

ApplicationContext eagerly creates singleton beans by default.

```
Application Starts

↓

Create Singleton Beans

↓

Inject Dependencies

↓

Initialize Beans

↓

Application Ready
```

So the first request doesn't pay the bean creation cost.

---

# Why Eager Initialization?

Suppose:

```
PaymentService
```

has an invalid dependency.

With eager initialization:

```
Startup

↓

Dependency Error

↓

Application Fails Fast
```

You discover the problem immediately.

With lazy creation,

the error may appear only when the bean is first used.

---

# Internal Hierarchy

```
               BeanFactory
                    │
                    ▼
         ApplicationContext
                    │
       ┌────────────┼────────────┐
       ▼            ▼            ▼
AnnotationConfig  WebApplication  Others
ApplicationContext   Context
```

Spring Boot commonly uses an implementation of `ApplicationContext` based on the type of application (for example, a web application uses a web-aware context).

---

# 4. BeanFactory vs ApplicationContext

| Feature | BeanFactory | ApplicationContext |
|----------|-------------|--------------------|
| IoC | ✅ | ✅ |
| Dependency Injection | ✅ | ✅ |
| Bean Lifecycle | ✅ | ✅ |
| Event Publishing | ❌ | ✅ |
| Message Source (i18n) | ❌ | ✅ |
| Resource Loading | Basic | Advanced |
| BeanPostProcessor Support | Basic | Automatic |
| Default Singleton Initialization | Lazy | Eager |

---

# Internal Startup Flow

```
SpringApplication.run()

↓

Create ApplicationContext

↓

Component Scan

↓

BeanDefinitions

↓

BeanFactory

↓

Instantiate Singleton Beans

↓

Dependency Injection

↓

BeanPostProcessors

↓

Lifecycle Callbacks

↓

Application Ready
```

Notice:

The BeanFactory is still present internally.

ApplicationContext builds on top of it.

---

# Production Usage

Modern Spring Boot applications almost always interact with `ApplicationContext`.

You may see it injected like this:

```java
@Autowired
private ApplicationContext applicationContext;
```

or

```java
UserService service =
    applicationContext.getBean(UserService.class);
```

Direct interaction with `BeanFactory` is uncommon in application code.

---

# Real Project Example

Imagine a payment application.

```
SpringApplication.run()

↓

ApplicationContext Created

↓

Component Scan

↓

PaymentController

↓

PaymentService

↓

PaymentRepository

↓

SecurityConfig

↓

Application Ready
```

Every bean is managed by the ApplicationContext.

---

# Best Practices

✅ Use `ApplicationContext` in modern Spring applications.

✅ Prefer dependency injection over calling `getBean()` manually.

✅ Treat the container as the owner of your beans.

✅ Avoid using `BeanFactory` directly unless working on framework-level code.

---

# Interview Perspective

## Question

What is the Spring Container?

Excellent Answer:

> The Spring Container is the core runtime component responsible for creating, configuring, injecting, initializing, and managing Spring beans throughout their lifecycle. It also provides services such as dependency injection, bean scopes, lifecycle management, and integration with other Spring features.

---

## Question

What is the difference between BeanFactory and ApplicationContext?

Excellent Answer:

> `BeanFactory` is the basic IoC container that provides core dependency injection and bean management. `ApplicationContext` extends `BeanFactory` by adding enterprise features such as event publishing, internationalization, resource loading, environment support, and automatic infrastructure integration. Spring Boot uses `ApplicationContext`.

---

## Question

Why does Spring Boot prefer ApplicationContext?

Excellent Answer:

> Spring Boot applications benefit from eager singleton initialization, automatic infrastructure registration, profile support, events, and many other enterprise features provided by `ApplicationContext`. These capabilities make it the standard choice for modern Spring applications.

---

# Common Interview Traps

### Trap 1

Is BeanFactory obsolete?

❌ No.

It remains the foundation of Spring's container hierarchy.

However, most applications interact with `ApplicationContext`.

---

### Trap 2

Does ApplicationContext replace BeanFactory?

❌ No.

It extends and builds upon it.

---

### Trap 3

Does ApplicationContext always create every bean eagerly?

❌ Not every bean.

By default, singleton beans are eagerly initialized.

Prototype beans and explicitly lazy beans follow different rules.

---

# Summary

Remember:

- The Spring Container manages beans.
- `BeanFactory` is the basic IoC container.
- `ApplicationContext` extends `BeanFactory`.
- Spring Boot uses `ApplicationContext`.
- Eager singleton initialization helps detect configuration problems early.

---

# Revision Cheat Sheet

```
Spring Container

↓

ApplicationContext

↓

BeanFactory

↓

BeanDefinitions

↓

Bean Creation

↓

Dependency Injection

↓

Bean Lifecycle

↓

Ready
```

---

# Exercises

1. What is the Spring Container?
2. Explain the responsibilities of the Spring Container.
3. Compare BeanFactory and ApplicationContext.
4. Why does Spring Boot use ApplicationContext?
5. Explain eager vs lazy bean initialization.

---

## End of Chapter 11

# Next Chapter (Final Module 1 Chapter)

🎯 **Module 1 Interview Master Revision**

We'll create:

- One-page revision notes
- Complete startup lifecycle diagram
- Top 50 interview questions with answers
- Common interviewer traps
- FAANG-style scenario questions
- "Explain Spring in 5 minutes" answer
- "Explain Spring internals" answer
- Complete Module 1 cheat sheet