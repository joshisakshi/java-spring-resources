# Module 1 – Spring Fundamentals

# Final Revision & Interview Handbook

---

# Goal

This chapter is your **last-minute revision guide** for Module 1.

Use it:

- One day before interviews
- Before online assessments
- Before system design rounds involving Spring
- Before revising Module 2

If you understand every point in this document, your Spring Core fundamentals are in excellent shape for SDE-1/SDE-2 backend interviews.

---

# Module 1 Mind Map

```
Spring Framework

│

├── Problems Before Spring

│

├── IoC

│      │

│      └── Dependency Injection

│

├── Spring Container

│      │

│      ├── BeanFactory

│      └── ApplicationContext

│

├── Bean

│      │

│      ├── Lifecycle

│      ├── Scopes

│      └── BeanDefinition

│

├── Component Scanning

│      │

│      ├── @Component

│      ├── @Service

│      ├── @Repository

│      ├── @Controller

│      └── @RestController

│

└── Java Configuration

       │

       ├── @Configuration

       └── @Bean
```

---

# Spring Startup Flow (Complete)

```
Application Starts

↓

Create ApplicationContext

↓

Read Configuration

↓

Component Scan

↓

Find Candidate Classes

↓

Create BeanDefinitions

↓

Register BeanDefinitions

↓

Instantiate Singleton Beans

↓

Dependency Injection

↓

BeanPostProcessor

↓

@PostConstruct

↓

Bean Ready

↓

Application Running
```

This is one of the most important diagrams in Spring.

---

# Complete Request Flow (Preview)

```
HTTP Request

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

Repository

↓

Service

↓

Controller

↓

HTTP Response
```

You'll study this in Module 3, but remember the flow now.

---

# Bean Lifecycle (Interview Version)

```
Instantiate Bean

↓

Populate Dependencies

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

Bean Ready

↓

Application Running

↓

@PreDestroy

↓

destroy()

↓

Bean Removed
```

---

# Bean Scopes

| Scope | Lifetime | Interview Importance |
|--------|----------|---------------------|
| Singleton | Entire Spring Container | ⭐⭐⭐⭐⭐ |
| Prototype | Every Lookup | ⭐⭐⭐⭐ |
| Request | Per HTTP Request | ⭐⭐⭐⭐ |
| Session | Per User Session | ⭐⭐⭐ |
| Application | ServletContext Lifetime | ⭐⭐ |
| WebSocket | WebSocket Session | ⭐ |

---

# Stereotype Annotations

| Annotation | Responsibility |
|------------|----------------|
| `@Component` | Generic Spring Bean |
| `@Service` | Business Logic |
| `@Repository` | Persistence Layer |
| `@Controller` | MVC Controller |
| `@RestController` | REST APIs |
| `@Configuration` | Java Configuration |

---

# Dependency Injection Types

| Type | Recommended? |
|--------|--------------|
| Constructor Injection | ✅ Best Practice |
| Setter Injection | Optional Dependencies |
| Field Injection | ❌ Avoid in production |

Remember:

> Constructor Injection is the industry standard.

---

# `@Component` vs `@Bean`

| `@Component` | `@Bean` |
|--------------|----------|
| Class Level | Method Level |
| Component Scan | Java Configuration |
| Own Classes | Third-party / Custom Construction |
| Automatic Discovery | Explicit Registration |

---

# BeanFactory vs ApplicationContext

| BeanFactory | ApplicationContext |
|--------------|--------------------|
| Basic IoC | Advanced IoC |
| Lazy Loading | Eager Singleton Loading |
| Minimal Features | Enterprise Features |
| Rarely Used Directly | Used by Spring Boot |

---

# Most Important Definitions

### Spring

A lightweight framework that provides IoC, Dependency Injection, AOP, transaction management, and enterprise infrastructure for Java applications.

---

### IoC

The control of object creation and dependency management is transferred from the application code to the Spring container.

---

### Dependency Injection

A technique in which the Spring container provides required dependencies to an object instead of the object creating them itself.

---

### Bean

An object whose lifecycle is managed by the Spring IoC container.

---

### BeanDefinition

Spring's internal metadata describing how a bean should be created and managed.

---

### Spring Container

The runtime environment responsible for creating, configuring, wiring, and managing Spring beans.

---

### Component Scanning

The process of discovering classes annotated with Spring stereotype annotations and registering them as bean definitions.

---

### `@Configuration`

Marks a class as a source of bean definitions.

---

### `@Bean`

Registers the object returned by a method as a Spring-managed bean.

---

# Top Interview Questions

## Basic

### Q1. What is Spring?

Expected Topics:

- Lightweight Framework
- IoC
- DI
- Enterprise Applications

---

### Q2. What problems did Spring solve?

Mention:

- Tight Coupling
- XML Hell
- Boilerplate
- Manual Object Creation
- Difficult Testing

---

### Q3. What is IoC?

Explain:

> Control of object creation moves from developer to container.

---

### Q4. What is Dependency Injection?

Explain:

> Dependencies are provided externally by the container rather than created using `new`.

---

### Q5. What is a Bean?

> A Spring-managed object.

---

### Q6. What is Bean Scope?

Discuss:

- Singleton
- Prototype
- Request
- Session

---

### Q7. Constructor vs Setter Injection?

Expected Answer:

Constructor Injection is preferred because it enforces required dependencies, supports immutability, and is easier to test.

---

### Q8. Difference between `@Component` and `@Service`?

Expected Answer:

Functionally similar, but `@Service` communicates business-layer intent.

---

### Q9. Difference between `@Component` and `@Bean`?

Focus on:

- Automatic discovery
- Explicit registration
- Third-party classes

---

### Q10. Difference between BeanFactory and ApplicationContext?

Focus on:

- Features
- Eager vs Lazy initialization
- Spring Boot usage

---

# Advanced Interview Questions

### Why is Constructor Injection preferred?

---

### How does Spring perform Component Scanning?

---

### What is a BeanDefinition?

---

### How does Spring create singleton beans?

---

### What happens during Spring startup?

---

### Why doesn't `@Bean` create multiple singleton objects?

---

### Explain CGLIB.

---

### Explain Bean Lifecycle.

---

### Explain Dependency Injection Internally.

---

### Explain the Spring Container.

---

# FAANG Scenario Questions

## Scenario 1

Your application starts but `PaymentService` is never created.

Possible causes:

- Missing stereotype annotation
- Package not scanned
- Conditional configuration
- Lazy initialization
- Bean creation failure

---

## Scenario 2

You receive:

```
NoSuchBeanDefinitionException
```

Possible reasons:

- Bean not registered
- Package outside component scan
- Missing configuration
- Wrong bean type
- Bean creation exception

---

## Scenario 3

A Singleton bean stores mutable user data.

Question:

What's wrong?

Expected Answer:

Singletons are shared across threads. Storing request- or user-specific mutable state can lead to race conditions and data leakage.

---

## Scenario 4

A third-party SDK object must be injected.

Question:

How?

Expected Answer:

Register it using a `@Bean` method inside a `@Configuration` class.

---

## Scenario 5

A controller contains all business logic.

Question:

How would you refactor it?

Expected Answer:

Move business logic to a `@Service`, keep the controller responsible for request handling and response generation.

---

# Common Interview Traps

❌ Spring creates beans immediately after component scanning.

✔ Component scanning creates **BeanDefinitions**. Bean instantiation happens later.

---

❌ Singleton means one object per JVM.

✔ Singleton scope means one object per Spring ApplicationContext.

---

❌ `@RestController` is unrelated to `@Controller`.

✔ `@RestController` is effectively `@Controller` + `@ResponseBody`.

---

❌ Field Injection is the recommended approach.

✔ Constructor Injection is preferred in production.

---

❌ BeanFactory is obsolete.

✔ ApplicationContext extends BeanFactory. BeanFactory remains the foundation of the container.

---

# 5-Minute Spring Explanation

> Spring is a lightweight Java framework that simplifies enterprise application development by implementing Inversion of Control (IoC) and Dependency Injection (DI). Instead of developers manually creating and wiring objects, the Spring container manages bean creation, dependency injection, lifecycle, scopes, and configuration. During startup, Spring scans configured packages, creates BeanDefinitions, instantiates singleton beans, injects dependencies, and initializes the application context. Features such as component scanning, Java configuration, and lifecycle management make applications loosely coupled, testable, and maintainable.

---

# One-Page Cheat Sheet

```
Spring

↓

IoC

↓

Dependency Injection

↓

ApplicationContext

↓

Component Scan

↓

BeanDefinition

↓

Singleton Bean

↓

Dependency Injection

↓

@PostConstruct

↓

Application Ready
```

Remember:

```
@Component

↓

Generic Bean

@Service

↓

Business Logic

@Repository

↓

Persistence

@RestController

↓

REST APIs

@Configuration

↓

@Bean
```

---

# Module 1 Complete ✅

You now understand:

- Why Spring was created
- IoC
- Dependency Injection
- Bean lifecycle
- Bean scopes
- Component Scanning
- Stereotype Annotations
- Java Configuration
- `@Bean`
- `@Configuration`
- CGLIB
- BeanFactory
- ApplicationContext
- Spring startup flow
- Core Spring internals

This foundation is enough to confidently begin Spring Boot.

---

# Next Module

## Module 2 – Spring Boot

We'll cover:

1. Why Spring Boot?
2. `@SpringBootApplication`
3. Spring Boot Startup Flow
4. Auto Configuration
5. Starter Dependencies
6. `application.properties`
7. `application.yml`
8. Profiles
9. `@ConfigurationProperties`
10. Embedded Tomcat
11. Actuator

These topics build directly on everything you've learned in Module 1.