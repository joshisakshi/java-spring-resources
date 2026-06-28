# Module 1 - Spring Fundamentals

# 04 - Spring Architecture

> Part 2

**Interview Frequency:** ⭐⭐⭐⭐⭐ (One of the Most Important Chapters)

---

# Goal

In Part 1, we saw Spring from the outside.

In this part, we're going to open the hood and see how Spring actually works internally.

This chapter is the bridge between Spring Architecture and the upcoming chapters on:

- IoC
- Dependency Injection
- BeanFactory
- ApplicationContext
- Bean Lifecycle

If you understand this chapter well, everything else in Spring starts making sense.

---

# Table of Contents

1. Internal View of Spring
2. What is the Core Container?
3. BeanFactory
4. BeanDefinition
5. Bean Registry
6. ApplicationContext
7. Complete Startup Flow
8. Interview Perspective
9. Summary

---

# Think Like Spring

Most developers think like this:

```
@Service

↓

Spring creates object
```

This is an oversimplification.

Spring actually thinks like this:

```
Scan Classes

↓

Find Components

↓

Create BeanDefinition

↓

Register BeanDefinition

↓

Instantiate Bean

↓

Inject Dependencies

↓

Initialize Bean

↓

Store Singleton

↓

Application Ready
```

Notice something.

The **object is NOT the first thing Spring creates.**

It first creates metadata.

This metadata is called a **BeanDefinition**.

---

# High-Level Internal Architecture

```
                 Spring Boot

                      │

                      ▼

           ApplicationContext

                      │

         ┌────────────┴────────────┐

         ▼                         ▼

 BeanFactory              Environment

         │

         ▼

 BeanDefinition Registry

         │

         ▼

 Bean Definitions

         │

         ▼

 Bean Objects (Singletons)

         │

         ▼

 Your Controllers

 Your Services

 Your Repositories
```

Everything revolves around one idea:

> **Spring manages metadata first, objects later.**

---

# The Core Container

Interview Frequency: ⭐⭐⭐⭐⭐

The Core Container is the heart of Spring.

Its responsibilities are:

- Reading configuration
- Discovering beans
- Creating beans
- Managing bean lifecycle
- Injecting dependencies
- Destroying beans

Think of it as a factory plus a manager.

Real-world analogy:

Imagine a hotel.

The hotel management:

- Registers rooms
- Assigns guests
- Cleans rooms
- Maintains records

Similarly,

Spring's Core Container:

- Registers beans
- Creates beans
- Injects dependencies
- Maintains lifecycle

---

# BeanFactory

Interview Frequency: ⭐⭐⭐⭐⭐

## Definition

BeanFactory is the **basic IoC container** in Spring.

It is responsible for creating and managing beans.

Think of it as:

```
BeanFactory

↓

Creates Objects

↓

Returns Objects

↓

Stores Objects
```

Very simplified interface:

```java
public interface BeanFactory {

    Object getBean(String name);

}
```

That's its primary responsibility.

---

# Why BeanFactory Exists

Suppose your application has:

```
UserService

OrderService

PaymentService

InventoryService
```

Without BeanFactory:

You would manually create every object.

With BeanFactory:

```
BeanFactory

↓

getBean()

↓

Returns Managed Object
```

Instead of:

```java
new UserService();
```

You ask Spring:

```java
context.getBean(UserService.class);
```

Spring returns the managed instance.

---

# Important Point

BeanFactory does **not** scan packages.

It does **not** automatically find components.

It only manages beans that have already been registered.

This distinction is very important.

---

# BeanDefinition

Interview Frequency: ⭐⭐⭐⭐⭐

This is one of the most misunderstood concepts.

Most developers think Spring immediately creates objects.

Not true.

Spring first creates a BeanDefinition.

---

## What is a BeanDefinition?

A BeanDefinition is simply metadata describing a bean.

Think of it like a blueprint.

```
House Blueprint

↓

Contains

Room Count

Door Count

Dimensions

Material

Owner

```

The blueprint is **not the actual house**.

Similarly,

```
BeanDefinition

↓

Class Name

Scope

Constructor

Dependencies

Init Method

Destroy Method

Lazy/Eager

Qualifiers
```

Again,

this is **not the object**.

It is only metadata.

---

# Internal Flow

Suppose Spring finds:

```java
@Service
public class UserService {

}
```

Spring internally creates something conceptually similar to:

```
BeanDefinition

Bean Name:

userService

Class:

UserService

Scope:

Singleton

Constructor:

Default Constructor

Lazy:

false
```

Only after collecting these definitions does Spring begin creating objects.

---

# Why Metadata First?

Imagine Spring created objects immediately.

Problem:

```
UserService

↓

Needs UserRepository

↓

Repository not discovered yet
```

Dependency resolution would fail.

Instead,

Spring performs two phases.

### Phase 1

```
Scan

↓

Register Metadata
```

### Phase 2

```
Create Objects

↓

Inject Dependencies
```

This two-phase approach is one of Spring's key architectural decisions.

---

# Bean Registry

Interview Frequency: ⭐⭐⭐⭐☆

After creating BeanDefinitions,

Spring stores them in a registry.

Think of it like a phone directory.

```
Bean Registry

│

├── userService

├── orderService

├── paymentService

├── inventoryService

└── dataSource
```

Each entry points to its corresponding BeanDefinition.

Notice:

Still no objects.

Only metadata.

---

# Object Creation Phase

Only after registration completes does Spring begin creating beans.

```
BeanDefinition

↓

Reflection

↓

Constructor Called

↓

Object Created

↓

Dependency Injection

↓

Initialization

↓

Singleton Cache
```

Notice the separation between:

Metadata

and

Objects.

---

# ApplicationContext

Interview Frequency: ⭐⭐⭐⭐⭐

Most interviews ask this.

## Definition

ApplicationContext is the **most commonly used IoC container in Spring.**

It extends BeanFactory and provides many additional enterprise features.

---

# Relationship

```
BeanFactory

↑

ApplicationContext
```

This means:

ApplicationContext can do everything BeanFactory can,

plus much more.

---

# Additional Features

ApplicationContext provides:

- Event Publishing
- Internationalization (i18n)
- Environment Support
- Resource Loading
- Automatic BeanPostProcessor Registration
- Automatic Component Scanning

This is why almost every Spring Boot application uses ApplicationContext instead of BeanFactory directly.

---

# Internal Hierarchy

```
ApplicationContext

↓

BeanFactory

↓

BeanDefinition Registry

↓

Bean Definitions

↓

Singleton Objects
```

Notice the layering.

ApplicationContext doesn't replace BeanFactory.

It builds on top of it.

---

# Complete Startup Flow

Putting everything together:

```
Application Starts

↓

Create ApplicationContext

↓

Scan Packages

↓

Find Components

↓

Create BeanDefinitions

↓

Register BeanDefinitions

↓

Instantiate Beans

↓

Inject Dependencies

↓

Initialize Beans

↓

Store Singleton Objects

↓

Application Ready
```

This is one of the most important diagrams in Spring.

Memorize it.

---

# Interviewer's Perspective

⭐⭐⭐⭐⭐

### Question

How does Spring create a bean?

Average Candidate:

> Spring sees @Component and creates an object.

Strong Candidate:

> During startup, Spring scans the classpath, creates BeanDefinitions for detected components, registers them in the BeanDefinitionRegistry, and later instantiates the beans through the BeanFactory. Dependencies are injected, lifecycle callbacks are executed, and singleton beans are stored for reuse.

That answer demonstrates actual understanding.

---

# Common Interview Traps

## Trap 1

**Question:**

Does `@Component` immediately create an object?

Correct Answer:

No.

It contributes metadata that eventually becomes a BeanDefinition.

Object creation happens later during container initialization.

---

## Trap 2

**Question:**

Does BeanFactory scan packages?

Correct Answer:

No.

Package scanning is handled by other Spring components.

BeanFactory manages bean creation and retrieval.

---

## Trap 3

**Question:**

Is BeanDefinition the bean itself?

Correct Answer:

No.

A BeanDefinition is metadata describing how a bean should be created.

---

# Real Project Perspective

Imagine a fintech application with:

```
WalletController

↓

WalletService

↓

BlockchainService

↓

WalletRepository
```

During startup, Spring first creates BeanDefinitions for all four classes.

Only after discovering the complete dependency graph does it instantiate the objects.

This prevents issues where one bean depends on another that hasn't yet been processed.

---

# Summary

You should now understand:

- Why Spring creates metadata before objects
- What a BeanDefinition is
- The purpose of BeanFactory
- The role of ApplicationContext
- The difference between metadata and actual bean instances
- Why Spring follows a two-phase startup process

---

# Revision Cheat Sheet

```
@Component

↓

BeanDefinition

↓

BeanDefinitionRegistry

↓

BeanFactory

↓

Create Bean

↓

Dependency Injection

↓

Initialization

↓

Singleton Cache

↓

Ready
```

---

# End of Part 2

## Next Part

We'll connect everything to a real Spring Boot application by exploring:

- Where `SpringApplication.run()` fits into the architecture
- How component scanning works at a high level
- How Spring discovers `@Component`, `@Service`, `@Repository`, and `@Controller`
- The role of reflection in bean creation
- How all the architecture pieces collaborate before the first HTTP request
- Production architecture in real backend services

> **Preview:** Starting from the next chapter (**IoC**), we'll begin referencing actual framework classes such as `DefaultListableBeanFactory`, `AbstractApplicationContext`, and `ClassPathBeanDefinitionScanner` to understand the real execution flow without turning this into a source code reading exercise.