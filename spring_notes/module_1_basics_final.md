# 01 - What is Spring?

> Part 4 (Final)

---

# 5. Spring Internals - What Actually Happens?

One of the biggest misconceptions about Spring is that annotations like `@Service`, `@Component`, or `@Autowired` somehow "magically work."

There is **no magic**.

Everything Spring does follows a sequence of well-defined steps.

Understanding this execution flow is extremely valuable for interviews.

---

# A Simple Spring Boot Application

Consider the smallest possible Spring Boot application.

```java
@SpringBootApplication
public class ShoppingApplication {

    public static void main(String[] args) {
        SpringApplication.run(ShoppingApplication.class, args);
    }

}
```

Looks simple.

Just one line.

```java
SpringApplication.run(...)
```

But internally, thousands of operations happen before your application is ready.

---

# High-Level Startup Flow

```
main()

↓

SpringApplication.run()

↓

Create SpringApplication Object

↓

Determine Application Type

↓

Create ApplicationContext

↓

Read Configuration

↓

Scan Packages

↓

Find Components

↓

Create Bean Definitions

↓

Instantiate Beans

↓

Inject Dependencies

↓

Initialize Beans

↓

Embedded Server Starts

↓

Application Ready
```

Notice something important.

Your controller hasn't even received a request yet.

Everything above happens **before the first HTTP request**.

---

# What Happens Inside SpringApplication.run()?

Although the actual implementation is much more complex, the process can be visualized as:

```java
public static ConfigurableApplicationContext run(...) {

    SpringApplication app = new SpringApplication();

    app.prepareEnvironment();

    app.createApplicationContext();

    app.refreshContext();

    app.finishStartup();

}
```

Each of these methods performs many internal operations.

---

# Step 1 — Spring Creates an IoC Container

One of Spring's first tasks is creating an object called the **ApplicationContext**.

Think of it as a huge registry.

```
ApplicationContext

│

├── Bean Definitions

├── Singleton Objects

├── Environment

├── Event Publisher

├── Resource Loader

└── Bean Factory
```

Everything Spring manages lives here.

---

# Step 2 — Component Scanning

Suppose your project looks like this.

```
com.company.project

│

├── controller

│      UserController

│

├── service

│      UserService

│

├── repository

│      UserRepository

│

└── config

       SecurityConfig
```

Spring begins scanning packages.

Whenever it encounters annotations like:

```java
@Component

@Service

@Repository

@Controller

@RestController
```

it creates metadata describing those classes.

Notice:

At this stage,

**objects are not yet created.**

Only metadata is collected.

---

# Step 3 — Bean Definitions

Spring stores information like this internally.

```
Bean Definition

Bean Name

↓

Class

↓

Scope

↓

Constructor

↓

Dependencies

↓

Lifecycle Methods

↓

Lazy/Eager

↓

Initialization Metadata
```

Think of a Bean Definition as a blueprint.

It is **not the actual object.**

Exactly like:

```
House Blueprint

≠

Actual House
```

---

# Step 4 — Object Creation

Once Spring understands every bean,

it begins instantiating them.

Example:

```
UserRepository

↓

new UserRepository()

↓

Stored in Container
```

Then,

```
UserService

↓

new UserService(repository)

↓

Stored
```

Then,

```
UserController

↓

new UserController(service)

↓

Stored
```

This order matters.

Dependencies must exist before dependent objects are created.

---

# Step 5 — Dependency Injection

Suppose we have:

```java
@Service
public class UserService {

    private final UserRepository repository;

    public UserService(UserRepository repository) {

        this.repository = repository;

    }

}
```

Spring thinks like this:

```
Need UserRepository?

↓

Already Exists?

↓

YES

↓

Pass into Constructor

↓

UserService Created
```

You never wrote:

```java
new UserRepository();
```

Spring did.

---

# Internal Dependency Graph

Spring internally builds something similar to this.

```
UserController

↓

UserService

↓

UserRepository

↓

DataSource

↓

Database
```

This graph allows Spring to determine:

- creation order
- dependency chain
- circular dependencies
- singleton reuse

---

# Step 6 — Bean Initialization

After object creation,

Spring performs additional work.

```
Object Created

↓

Inject Dependencies

↓

@PostConstruct

↓

BeanPostProcessor

↓

AOP Proxy

↓

Ready
```

Many interview candidates think the bean is ready immediately after calling the constructor.

Not true.

Initialization continues after construction.

We'll dedicate an entire chapter to this lifecycle.

---

# Step 7 — Embedded Server Starts

If this is a web application,

Spring Boot starts Tomcat.

```
ApplicationContext Ready

↓

Embedded Tomcat

↓

Port 8080

↓

Listening

↓

HTTP Requests Accepted
```

Now your application is finally ready.

---

# First Request Lifecycle

Suppose the user opens:

```
GET /users/10
```

Execution flow:

```
Browser

↓

Tomcat

↓

DispatcherServlet

↓

Handler Mapping

↓

UserController

↓

UserService

↓

UserRepository

↓

Database

↓

Repository

↓

Service

↓

Controller

↓

HTTP Response

↓

Browser
```

Every request follows a similar path.

---

# Where Are Beans Stored?

A common interview question.

Beans are ordinary Java objects.

They live in the JVM Heap.

```
JVM

Heap

│

├── ApplicationContext

│      │

│      ├── UserController

│      ├── UserService

│      ├── UserRepository

│      └── DataSource

│

└── Other Objects
```

The ApplicationContext stores references to these bean instances.

It does **not** create a separate memory region.

---

# Why Spring Is So Popular

Spring solved multiple software engineering challenges simultaneously.

## Before Spring

```
Developer

↓

Creates Objects

↓

Configures Objects

↓

Injects Dependencies

↓

Handles Transactions

↓

Creates Connections

↓

Writes Business Logic
```

The developer was responsible for everything.

---

## With Spring

```
Developer

↓

Writes Business Logic

↓

Spring

↓

Creates Objects

Injects Dependencies

Manages Transactions

Creates Proxies

Handles Lifecycle

Configuration

Caching

Security

Logging
```

Developers focus on solving business problems.

Spring handles infrastructure.

---

# Real Production Example

Imagine an e-commerce company.

```
Order Service

↓

Inventory Service

↓

Payment Service

↓

Notification Service

↓

Kafka

↓

Database

↓

Redis
```

Without Spring,

developers would manually create every dependency.

With Spring,

the container builds the dependency graph automatically.

When hundreds of services exist,

this becomes invaluable.

---

# Best Practices

## Prefer Constructor Injection

Good:

```java
@Service
public class UserService {

    private final UserRepository repository;

    public UserService(UserRepository repository) {

        this.repository = repository;

    }

}
```

Avoid:

```java
@Autowired

private UserRepository repository;
```

Constructor injection makes dependencies explicit, supports immutability, and improves testability.

---

## Program to Interfaces

Prefer:

```java
PaymentService service;
```

instead of

```java
StripePaymentService service;
```

This allows implementations to change without affecting consumers.

---

## Keep Business Logic Independent

Your service classes should not depend on framework-specific APIs unless necessary.

Aim to write business logic as plain Java code.

---

# Common Mistakes

### Mistake 1

Thinking Spring creates every object automatically.

Reality:

Only registered beans are managed.

---

### Mistake 2

Believing `@Autowired` creates objects.

Reality:

It only injects already-created beans.

---

### Mistake 3

Using `new` for Spring-managed classes.

Example:

```java
UserService service = new UserService();
```

Spring is bypassed.

Dependency injection, AOP, transactions, and lifecycle callbacks will not work.

---

### Mistake 4

Thinking Spring Boot and Spring Framework are the same.

Spring Boot simplifies configuration.

Spring Framework provides the underlying features.

---

# Interview Questions

## Basic

### What is Spring?

### Why is Spring called lightweight?

### What problems did Spring solve?

### Why is Spring considered modular?

---

## Intermediate

### Explain the Spring startup process.

### What happens inside `SpringApplication.run()`?

### Where are Spring beans stored?

### What is the difference between a Bean Definition and a Bean Instance?

---

## Advanced

### Explain the complete lifecycle from `main()` to the first HTTP request.

### How does Spring determine bean creation order?

### Why does Spring need an ApplicationContext?

### How does Spring know which classes to instantiate?

### Why are constructor dependencies preferred?

### What happens if Spring cannot resolve a dependency?

---

# Chapter Summary

After completing this chapter, you should understand:

- Why Spring exists
- Historical problems with Java EE
- Spring's philosophy
- Major Spring modules
- High-level architecture
- Startup process
- Bean discovery
- Bean definitions
- Bean creation
- Dependency graph
- ApplicationContext
- Request flow
- Production significance

This foundation is essential before learning IoC, Dependency Injection, Beans, and Spring Boot internals.

---

# Quick Cheat Sheet

```
Spring

↓

Framework

↓

IoC Container

↓

Creates Beans

↓

Injects Dependencies

↓

Manages Lifecycle

↓

Starts Application

↓

Handles Requests

↓

Business Logic Executes
```

### Remember

- Spring manages objects.
- Managed objects are called Beans.
- Beans live inside the IoC Container.
- ApplicationContext is the most commonly used container.
- Spring Boot starts the container automatically.
- Spring Boot is built on top of the Spring Framework.

---

# Exercises

## Conceptual

1. Explain Spring without using the words "framework" or "dependency injection."
2. Draw the Spring architecture from memory.
3. Explain the startup flow from `main()` to the first request.
4. Differentiate Bean Definition and Bean Instance.

## Coding

1. Create a simple Spring Boot project.
2. Add one Controller.
3. Add one Service.
4. Add one Repository.
5. Observe how Spring injects dependencies using constructor injection.
6. Replace constructor injection with manual `new` and observe the differences.

---

# End of Chapter

You have now completed **01 - What is Spring?**

In the next chapter, **02 - Problems Before Spring**, we'll go even deeper into:

- Enterprise Java before Spring
- EJB architecture
- XML Hell
- Tight coupling
- Manual object creation
- Why Dependency Injection became revolutionary
- Comparison with .NET and other frameworks of that era
- How these problems influenced Spring's design
- Interview questions focused on legacy Java systems