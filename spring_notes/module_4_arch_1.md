# Module 1 - Spring Fundamentals

# 04 - Spring Architecture

> Part 1

**Interview Frequency:** ⭐⭐⭐⭐⭐ (Very Frequently Asked)

> **Why this chapter matters**
>
> If IoC is the **heart** of Spring, then **Spring Architecture** is the **anatomy**.
>
> Most candidates know annotations like `@Service` and `@Autowired`.
>
> Strong candidates know **why they work**.
>
> This chapter builds the mental model you'll use for the rest of Spring Boot.

---

# Goal

After this chapter, you should be able to answer:

- What is Spring Architecture?
- What are the major Spring modules?
- Which module is the most important?
- How do Spring modules communicate?
- What is the Core Container?
- Where does IoC fit into the architecture?
- Which modules are commonly used in Spring Boot?
- How does a request eventually reach the database?

---

# Table of Contents

1. What is Spring Architecture?
2. Why is Spring Modular?
3. High-Level Architecture
4. Core Spring Modules
5. Core Container
6. Data Flow Between Modules
7. How Spring Applications Are Layered
8. Real Project Architecture
9. Interview Perspective
10. Summary

---

# 1. What is Spring Architecture?

## Definition

Spring Architecture refers to the organization of the Spring Framework into multiple independent modules that work together to build enterprise applications.

Instead of creating one gigantic framework, Spring is divided into specialized modules.

Think of it as building blocks.

```
          Spring Framework

      ┌──────────────────────┐
      │     Spring Core      │
      └──────────────────────┘
                 │
 ┌───────────────┼─────────────────┐
 │               │                 │
 ▼               ▼                 ▼
 MVC          Security         Data JPA

 │               │                 │

 └───────────────┼─────────────────┘
                 │
                 ▼
          Your Application
```

Each module has a specific responsibility.

---

# Why is this architecture important?

Imagine if Spring were a single huge library.

Every project would have to include:

- MVC
- Security
- Kafka
- Redis
- WebSockets
- Batch Processing
- Reactive Programming

Even if you only wanted to create a REST API.

That would mean:

- Larger application size
- Slower startup
- Higher memory usage
- Unnecessary dependencies

Instead, Spring follows a modular architecture.

You include only what you need.

---

# Real-World Analogy

Imagine constructing a house.

```
House

↓

Foundation

↓

Walls

↓

Electricity

↓

Plumbing

↓

Painting
```

You don't build everything at once.

Each team specializes in one area.

Similarly,

Spring has specialized modules.

```
Spring

↓

Core

↓

MVC

↓

Data

↓

Security

↓

AOP
```

Each module performs one responsibility well.

---

# 2. Spring Modules

The Spring Framework consists of several major modules.

```
Spring Framework

│

├── Core Container

├── AOP

├── Data Access

├── Web (MVC)

├── Security

├── Testing

├── Messaging

├── Integration

└── Others
```

For interviews, focus on these six:

```
Core

MVC

Data

Security

AOP

Test
```

These appear in almost every backend project.

---

# Module 1 - Core Container ⭐⭐⭐⭐⭐

This is the most important module.

Everything else depends on it.

Responsibilities:

- Bean Creation
- Dependency Injection
- IoC
- Bean Lifecycle
- Configuration
- Bean Management

Without the Core Container,

Spring cannot function.

Think of it as the operating system of Spring.

---

# Core Container Components

Internally,

the Core Container itself is divided into smaller parts.

```
Core Container

│

├── spring-core

├── spring-beans

├── spring-context

└── spring-expression (SpEL)
```

Let's understand each.

---

## spring-core

Provides the fundamental infrastructure.

Responsibilities include:

- Utility classes
- Core abstractions
- Resource loading
- Type conversion
- Reflection utilities

Think of it as the lowest layer.

---

## spring-beans

Responsible for:

- Bean creation
- Bean definitions
- Bean metadata
- Dependency management

This module is where Spring starts becoming interesting.

Later we'll study classes like:

```
BeanDefinition

BeanFactory

DefaultListableBeanFactory
```

These belong here.

---

## spring-context

Probably the most frequently used module.

It provides:

- ApplicationContext
- Event publishing
- Resource loading
- Internationalization
- Environment support

Whenever you write:

```java
ApplicationContext context;
```

you're using this module.

---

## spring-expression (SpEL)

Provides the Spring Expression Language.

Example:

```java
@Value("#{2 + 3}")
```

Interview Frequency:

⭐☆☆☆☆

Know that it exists.

No need to master it for SDE-1/SDE-2 interviews.

---

# Module 2 - Spring MVC ⭐⭐⭐⭐⭐

This module handles web requests.

Responsibilities:

- REST APIs
- Controllers
- Request Mapping
- JSON Conversion
- Validation
- HTTP Responses

Architecture:

```
Browser

↓

DispatcherServlet

↓

Controller

↓

Service

↓

Repository

↓

Database

↓

Response
```

We'll dedicate an entire module to Spring MVC later.

---

# Module 3 - Spring Data

Purpose:

Simplify database interaction.

Without Spring Data:

```
Connection

↓

PreparedStatement

↓

Execute Query

↓

ResultSet

↓

Mapping

↓

Close Connection
```

With Spring Data:

```java
userRepository.findById(id);
```

Huge difference.

Spring generates most of the implementation.

---

# Module 4 - Spring Security

Handles:

- Login
- Authentication
- Authorization
- JWT
- OAuth2
- Password Encoding
- Session Management

Instead of writing authentication yourself,

Spring Security provides production-ready implementations.

---

# Module 5 - Spring AOP

AOP stands for

Aspect-Oriented Programming.

Suppose every service needs logging.

Without AOP:

```
Logging

↓

Business Logic

↓

Logging
```

Repeated everywhere.

With AOP:

```
Aspect

↓

Business Method

↓

Aspect
```

Cross-cutting concerns are centralized.

Examples:

- Logging
- Transactions
- Security Checks
- Performance Monitoring

---

# Module 6 - Spring Test

Provides testing support.

Includes:

- JUnit Integration
- Mockito Support
- Test Context
- MockMvc
- Slice Testing

Testing is one of Spring's strengths.

---

# How Do These Modules Work Together?

Imagine a user sends a login request.

```
HTTP Request

↓

Spring MVC

↓

Controller

↓

Service

↓

Repository

↓

Spring Data

↓

Database
```

Meanwhile:

```
Spring Security

↓

Authenticate User

↓

Authorize Request

↓

Allow Controller
```

And:

```
Spring AOP

↓

Open Transaction

↓

Business Logic

↓

Commit Transaction
```

Notice something.

Different modules perform different jobs,

but they collaborate seamlessly.

---

# Core Container is the Foundation

Every module ultimately depends on the Core Container.

```
                 Spring Framework

                     │

          ┌──────────┴──────────┐

          ▼                     ▼

    Spring MVC          Spring Security

          │                     │

          └──────────┬──────────┘

                     ▼

              Core Container

                     │

      Bean Creation & Dependency Injection
```

Without beans,

none of the higher-level modules can function.

---

# Typical Backend Architecture

Most production Spring Boot applications follow this layered architecture.

```
Controller Layer

↓

Service Layer

↓

Repository Layer

↓

Database
```

Example:

```
UserController

↓

UserService

↓

UserRepository

↓

MySQL
```

Spring manages every object in these layers.

---

# Interviewer's Perspective

⭐⭐⭐⭐⭐

**Interviewer:** Explain Spring Architecture.

### Average Answer

> Spring has modules like MVC, Security, and Data.

This is incomplete.

### Strong Answer

> Spring follows a modular architecture where the Core Container forms the foundation. It manages beans, dependency injection, and lifecycle. Higher-level modules like MVC, Data JPA, Security, and AOP depend on the Core Container to obtain managed beans and collaborate to provide enterprise application functionality.

That answer demonstrates understanding rather than memorization.

---

# Common Interview Trap

### Question

Which module is the most important?

Many candidates answer:

> Spring MVC

Incorrect.

Correct answer:

> The Core Container.

Because every other module depends on it.

---

# Real Project Perspective

Consider a typical fintech service.

```
WalletController

↓

WalletService

↓

BlockchainService

↓

WalletRepository

↓

PostgreSQL
```

Additional modules:

```
Security

↓

JWT Authentication

↓

Logging Aspect

↓

Redis Cache

↓

Kafka Events
```

Although many modules participate,

every object is still created and managed by the Core Container.

---

# Summary

In this chapter, you learned:

- Why Spring is modular
- Major Spring modules
- Responsibilities of each module
- Why the Core Container is the foundation
- How modules interact
- Typical production architecture

---

# Cheat Sheet

```
Spring Framework

│

├── Core Container ⭐⭐⭐⭐⭐

├── MVC ⭐⭐⭐⭐⭐

├── Data ⭐⭐⭐⭐⭐

├── Security ⭐⭐⭐⭐⭐

├── AOP ⭐⭐⭐⭐☆

└── Test ⭐⭐⭐☆☆
```

**Remember:**

Everything eventually depends on the Core Container.

---

## End of Part 1

### Next Part

We'll go much deeper into the **Core Container** itself.

We'll study:

- What exactly is the IoC Container?
- BeanFactory
- ApplicationContext
- BeanDefinition
- Bean Registry
- DefaultListableBeanFactory
- Why Spring's architecture revolves around beans

> ⭐ This is where we'll begin understanding Spring almost from the framework's source-code perspective, while keeping it interview-oriented.