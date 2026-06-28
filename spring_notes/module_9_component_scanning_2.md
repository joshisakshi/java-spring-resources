# Module 1 - Spring Fundamentals

# Chapter 09 - Component Scanning & Stereotype Annotations

## Part 2 — `@Component`, `@Service`, `@Repository`, `@Controller`, `@RestController`

---

# Interview Frequency

⭐⭐⭐⭐⭐ (Extremely High)

Almost every Spring Boot interview includes one or more questions from this chapter.

Common Questions:

- Difference between `@Component` and `@Service`
- Difference between `@Controller` and `@RestController`
- Why do we need `@Repository`?
- Are all stereotype annotations the same internally?
- Which annotation should be used where?

---

# Goal

By the end of this chapter, you should understand:

- What stereotype annotations are
- Purpose of each stereotype annotation
- Internal implementation
- Semantic vs functional differences
- Production usage
- Interview traps
- Best practices

---

# Table of Contents

1. What are Stereotype Annotations?
2. `@Component`
3. `@Service`
4. `@Repository`
5. `@Controller`
6. `@RestController`
7. Internal Relationship
8. Real Project Structure
9. Best Practices
10. Interview Questions

---

# 1. What are Stereotype Annotations?

A **stereotype annotation** tells Spring:

> "This class has a specific role in the application and should be discovered during component scanning."

Instead of writing:

```java
@Bean
public UserService userService() {
    return new UserService();
}
```

you simply annotate the class:

```java
@Service
public class UserService {

}
```

Spring automatically discovers it and creates a bean.

---

# Common Stereotype Annotations

```
@Component

↓

Generic Bean

-------------------

@Service

↓

Business Logic

-------------------

@Repository

↓

Database Layer

-------------------

@Controller

↓

MVC Controller

-------------------

@RestController

↓

REST APIs
```

---

# 2. `@Component`

## Definition

`@Component` is the most generic stereotype annotation.

It simply marks a class as a Spring-managed bean.

Example:

```java
@Component
public class EmailValidator {

}
```

During startup:

```
Component Scan

↓

@Component Found

↓

BeanDefinition

↓

Bean Created
```

---

# When Should You Use `@Component`?

Use it for classes that do not naturally belong to another layer.

Examples:

- Utility classes
- Validators
- Converters
- Mappers
- Custom processors

Example:

```java
@Component
public class JwtTokenParser {

}
```

---

# 3. `@Service`

## Definition

`@Service` represents the **business logic layer**.

Example:

```java
@Service
public class PaymentService {

}
```

Typical responsibilities:

- Business rules
- Calculations
- Orchestration
- Calling repositories
- Calling external APIs

---

# Typical Flow

```
Controller

↓

Service

↓

Repository

↓

Database
```

Business logic belongs here.

Not inside controllers.

---

# Why Not Use `@Component` Everywhere?

Technically, you could.

But using `@Service` makes the code self-documenting.

When another developer sees:

```java
@Service
```

they immediately know:

"This class contains business logic."

---

# 4. `@Repository`

## Definition

`@Repository` represents the persistence layer.

Example:

```java
@Repository
public class UserRepository {

}
```

Responsibilities:

- Database access
- SQL execution
- JPA operations
- CRUD operations

---

# Internal Benefit

Unlike `@Component`, `@Repository` has an additional responsibility:

**Exception Translation**.

Suppose the database throws a vendor-specific exception.

For example:

```
SQLException
```

Spring can translate it into a consistent exception hierarchy, such as a `DataAccessException`, making your code less dependent on the underlying database technology.

Conceptually:

```
Database Exception

↓

@Repository

↓

Spring DataAccessException
```

This gives you database-independent exception handling.

---

# 5. `@Controller`

## Definition

Represents the web layer in a traditional Spring MVC application.

Example:

```java
@Controller
public class UserController {

}
```

Responsibilities:

- Receive HTTP requests
- Validate input
- Call services
- Return a view name

Example:

```java
@GetMapping("/home")
public String home() {

    return "home";

}
```

Spring interprets `"home"` as the name of a view (for example, a Thymeleaf template).

---

# 6. `@RestController`

Definition:

Designed for REST APIs.

Example:

```java
@RestController
public class UserController {

    @GetMapping("/users")
    public List<User> getUsers() {

        return service.getUsers();

    }

}
```

Instead of returning a view,

Spring converts the returned object into JSON (or another configured format) and writes it to the HTTP response body.

---

# `@Controller` vs `@RestController`

| Feature | `@Controller` | `@RestController` |
|----------|---------------|-------------------|
| Returns | View name by default | Response body (JSON/XML, etc.) |
| Typical Usage | MVC web applications | REST APIs |
| Requires `@ResponseBody`? | Yes, for REST responses | No |

---

# Internal Implementation

One of the most common interview questions:

What is `@RestController` internally?

Answer:

```java
@RestController
```

is effectively equivalent to:

```java
@Controller
@ResponseBody
```

Spring treats it as a controller where every handler method writes directly to the response body.

---

# Internal Relationship

A very important concept:

```
@Component

├─────────────┐
│             │
▼             ▼

@Service   @Repository

▼             ▼

Business   Persistence

        ▼

@Controller

        ▼

@RestController
```

Conceptually, all stereotype annotations are specializations of `@Component`.

This is why component scanning discovers all of them.

---

# Production Layered Architecture

```
                HTTP Request
                     │
                     ▼
          +--------------------+
          |   @RestController  |
          +--------------------+
                     │
                     ▼
          +--------------------+
          |      @Service      |
          +--------------------+
                     │
                     ▼
          +--------------------+
          |    @Repository     |
          +--------------------+
                     │
                     ▼
                 Database

Utilities

↓

@Component
```

This is the architecture you'll see in most Spring Boot applications.

---

# Which Annotation Should You Use?

| Class Type | Annotation |
|------------|------------|
| Business Logic | `@Service` |
| Database Access | `@Repository` |
| REST Endpoint | `@RestController` |
| MVC Controller | `@Controller` |
| Utility / Helper | `@Component` |

---

# Best Practices

✅ Use the annotation that matches the class's responsibility.

✅ Keep controllers thin.

✅ Place business logic in services.

✅ Keep repositories focused on data access.

✅ Reserve `@Component` for generic infrastructure classes.

---

# Interview Perspective

## Question

What is the difference between `@Component` and `@Service`?

Excellent Answer:

> Functionally, both are Spring stereotype annotations and both result in Spring-managed beans. The difference is semantic. `@Service` clearly communicates that the class belongs to the business layer, improving readability and maintainability.

---

## Question

Why use `@Repository` instead of `@Component`?

Excellent Answer:

> `@Repository` clearly identifies persistence-layer classes and also participates in Spring's exception translation mechanism, converting low-level persistence exceptions into Spring's `DataAccessException` hierarchy.

---

## Question

What is `@RestController` internally?

Excellent Answer:

> `@RestController` is a composed annotation that combines `@Controller` and `@ResponseBody`, causing every request-handling method to write its return value directly to the HTTP response body.

---

# Common Interview Traps

### Trap 1

Do `@Service` and `@Component` create different kinds of beans?

❌ No.

They both create Spring-managed beans.

The primary difference is semantic intent (with some annotations, such as `@Repository`, also enabling additional framework behavior).

---

### Trap 2

Can I annotate everything with `@Component`?

Technically, yes.

Practically, no.

Use the annotation that reflects the layer and responsibility of the class.

---

### Trap 3

Does `@Controller` automatically return JSON?

❌ No.

Use `@RestController` or `@ResponseBody` for REST responses.

---

# Summary

Remember:

- All stereotype annotations are discovered during component scanning.
- `@Component` is the generic stereotype.
- `@Service` represents business logic.
- `@Repository` represents persistence and supports exception translation.
- `@Controller` is used for MVC.
- `@RestController` is used for REST APIs.

---

# Revision Cheat Sheet

```
@Component

↓

Generic Bean

-------------------

@Service

↓

Business Logic

-------------------

@Repository

↓

Persistence Layer

-------------------

@Controller

↓

MVC Views

-------------------

@RestController

↓

REST APIs (JSON/XML)
```

---

# Exercises

1. Explain the differences between all stereotype annotations.
2. Why is `@Repository` preferred over `@Component` for persistence classes?
3. What is `@RestController` internally?
4. Can all stereotype annotations be discovered by component scanning?
5. Design the stereotype annotations for an e-commerce application with Controller, Service, Repository, and utility classes.

---

## End of Chapter 09

# Next Chapter

⭐⭐⭐⭐⭐ **`@Configuration` & `@Bean`**

We'll answer:

- Why do we need `@Bean` if we already have `@Component`?
- When should we use Java configuration?
- How does Spring process `@Configuration` classes?
- What is the difference between `@Bean` and `@Component`?
- How does Spring ensure `@Bean` methods return singletons?
- What are CGLIB proxies, and why are they used for configuration classes?

This is one of the most frequently asked Spring internals topics in SDE-2 and product-company interviews.