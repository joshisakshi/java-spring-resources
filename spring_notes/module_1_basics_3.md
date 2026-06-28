# 01 - What is Spring?

> Part 3

---

# 4. Spring Architecture Overview

Before we start learning IoC, Dependency Injection, Beans, or Spring Boot, it's important to understand the overall architecture of the Spring Framework.

One mistake many developers make is learning annotations like `@Service`, `@Autowired`, or `@Component` without understanding what happens behind the scenes.

Interviewers at product companies often expect you to answer questions like:

> "Where exactly does Spring create a bean?"

or

> "How does Spring know which classes to instantiate?"

To answer these confidently, you need to understand the architecture first.

---

# High-Level Architecture

At a very high level, Spring acts as a layer between your application and the underlying Java platform.

```
                Your Application
        (Controllers, Services, Repositories)
                        │
                        ▼
              Spring Framework
 ┌───────────────────────────────────────┐
 │ IoC Container                         │
 │ Dependency Injection                  │
 │ Spring MVC                            │
 │ Spring Data                           │
 │ Spring Security                       │
 │ Spring AOP                            │
 │ Spring Transaction Management         │
 └───────────────────────────────────────┘
                        │
                        ▼
               Java Virtual Machine
                        │
                        ▼
               Operating System
                        │
                        ▼
                 Physical Hardware
```

Spring does **not replace Java**.

Instead, it builds on top of Java and provides additional capabilities that simplify enterprise application development.

---

# Major Modules of Spring Framework

Spring is **modular**.

You don't have to use every feature.

Instead, you include only the modules your application requires.

```
Spring Framework

│

├── Core Container

├── Spring Beans

├── Spring Context

├── Spring Expression Language

├── Spring AOP

├── Spring MVC

├── Spring JDBC

├── Spring ORM

├── Spring Transaction

├── Spring Test

├── Spring Messaging

└── Spring WebFlux
```

Think of Spring as a toolbox.

You only pick the tools you need.

---

# 1. Core Container

This is the heart of Spring.

Without it, nothing else works.

Responsibilities:

- Creates objects
- Stores objects
- Injects dependencies
- Manages lifecycle
- Reads configuration
- Manages scopes

Everything starts here.

Later we'll study:

- BeanFactory
- ApplicationContext
- BeanDefinition
- BeanPostProcessor

These all belong to the Core Container.

---

# 2. Spring Beans Module

Responsible for managing Java objects.

Spring calls managed objects:

> **Beans**

Example:

```java
@Service
public class UserService {

}
```

This class becomes a Spring Bean.

Internally Spring stores metadata like:

```
Bean Name

↓

Bean Type

↓

Constructor

↓

Dependencies

↓

Scope

↓

Lifecycle
```

This metadata is called a **Bean Definition**.

We'll study Bean Definitions in detail later.

---

# 3. Spring Context

ApplicationContext is one of the most important classes in Spring.

It provides:

- Bean management
- Event handling
- Resource loading
- Internationalization
- Environment management

Almost every Spring Boot application starts by creating an ApplicationContext.

```
main()

↓

SpringApplication.run()

↓

ApplicationContext

↓

All Beans Created

↓

Application Ready
```

---

# 4. Spring AOP

AOP stands for:

> Aspect Oriented Programming

Suppose you have:

```
Order Service

Payment Service

Inventory Service

Customer Service
```

Each service needs:

- Logging
- Security
- Transactions

Without AOP:

```
Logging

Business Logic

Logging

Business Logic

Logging

Business Logic
```

Repeated everywhere.

Spring AOP removes this duplication.

Instead:

```
Logging Aspect

↓

Business Method

↓

Logging Aspect
```

This is achieved internally using proxies.

We'll dedicate an entire module to AOP later.

---

# 5. Spring MVC

MVC stands for:

Model

View

Controller

Spring MVC handles HTTP requests.

Example:

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

The famous `DispatcherServlet` acts as the front controller.

Every HTTP request first reaches the DispatcherServlet.

---

# 6. Spring Data

Spring Data simplifies database access.

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

Object Mapping

↓

Close Resources
```

Lots of boilerplate.

With Spring Data:

```java
userRepository.findById(id);
```

Spring generates most of the implementation automatically.

---

# 7. Spring Security

Spring Security handles:

- Login
- Authentication
- Authorization
- JWT
- OAuth2
- Password Encryption
- Session Management
- CSRF Protection

Instead of writing authentication from scratch, developers configure Spring Security.

---

# 8. Spring Test

Testing support includes:

- JUnit Integration
- Mockito
- MockMvc
- Test Slices
- Integration Testing

Spring allows testing individual layers without starting the entire application.

---

# How These Modules Work Together

Imagine a login request.

```
User

↓

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

↓

Response
```

Meanwhile,

```
Spring Security

↓

Authenticates User

↓

Allows Request

↓

Spring MVC Continues
```

And,

```
Spring AOP

↓

Starts Transaction

↓

Business Logic

↓

Commit Transaction
```

All modules collaborate seamlessly.

---

# Internal View of a Spring Application

When Spring starts, it builds an internal graph of all your application's components.

```
ApplicationContext

│

├── UserController

│       │

│       ▼

├── UserService

│       │

│       ▼

├── UserRepository

│       │

│       ▼

└── DataSource
```

Notice something?

Spring already knows:

- Which object depends on which
- Which object should be singleton
- Which constructor to call
- Which annotations are present

This graph is called the **Dependency Graph**.

One of Spring's startup tasks is building this graph.

---

# Spring Framework vs Spring Boot

Interviewers often ask this.

| Spring Framework | Spring Boot |
|------------------|-------------|
| Framework | Built on Spring |
| Requires manual configuration | Auto Configuration |
| No embedded server | Embedded Tomcat/Jetty |
| More setup | Convention over Configuration |
| XML or Java Config | Mostly annotation-based |

A useful analogy:

- **Spring Framework** is the engine.
- **Spring Boot** is a fully assembled car using that engine.

Spring Boot does **not replace** Spring Framework—it builds on it.

---

# Why Is Spring Modular?

Imagine every project had to include:

- MVC
- Security
- Messaging
- Batch Processing
- Reactive Programming
- WebSockets

Even if you only needed a REST API.

That would waste memory and increase startup time.

Instead, Spring lets you include only what you need.

For example:

```
spring-web

spring-context

spring-beans

spring-core
```

A simple REST API doesn't need messaging or batch processing.

This modularity keeps applications lightweight.

---

# Interview Tips

### Question:

**Why is Spring called a lightweight framework?**

A common answer is:

> "Because it's small."

This is incorrect.

A better answer:

> Spring is called lightweight because it allows developers to use only the required modules, encourages POJOs instead of heavyweight framework classes, and avoids forcing applications to run inside complex enterprise containers like traditional EJB servers.

---

### Question:

**What is the heart of Spring?**

Answer:

> The Core Container, specifically the IoC Container, because it manages bean creation, dependency injection, configuration, and lifecycle management.

---

### Question:

**Which Spring module receives HTTP requests?**

Answer:

> Spring MVC, through the `DispatcherServlet`, which acts as the Front Controller.

---

# Key Takeaways

- Spring is a collection of independent modules.
- The Core Container is the foundation of the framework.
- Beans are ordinary Java objects managed by Spring.
- ApplicationContext coordinates the application.
- Spring MVC handles web requests.
- Spring Data simplifies persistence.
- Spring Security manages authentication and authorization.
- Spring AOP handles cross-cutting concerns like logging and transactions.
- Spring Boot builds on top of Spring Framework—it doesn't replace it.

---

## What's Next?

In **Part 4**, we'll begin transitioning from the high-level architecture to the core concept that powers everything in Spring:

- **What is the IoC Container?**
- Why Spring is called an IoC framework
- How Spring starts from `main()`
- What happens inside `SpringApplication.run()`
- How beans are discovered and registered
- Internal startup flow (high-level)
- ASCII execution diagrams
- First look at Bean Definitions and the Bean Registry

This is where we'll start peeling back the "magic" behind Spring and understand what really happens under the hood.