# Module 7 — Spring Boot Internals (Last-Minute Interview Notes)

> **Interview Frequency:** ⭐⭐⭐⭐⭐
>
> **Purpose:** 90-minute revision before interviews.
>
> Covers almost every Spring internal question commonly asked in SDE-1/SDE-2 interviews.

---

# 1. Spring Boot Startup Flow ⭐⭐⭐⭐⭐

```
main()

↓

SpringApplication.run()

↓

Create ApplicationContext

↓

Read @SpringBootApplication

↓

@ComponentScan

↓

Create Beans

↓

Dependency Injection

↓

Embedded Tomcat Starts

↓

Application Ready
```

### Interview Answer

`SpringApplication.run()` bootstraps the Spring application by creating the IoC container, scanning components, creating beans, performing dependency injection, applying auto-configuration, and starting the embedded server.

---

# 2. Bean Creation Lifecycle ⭐⭐⭐⭐⭐

```
Class Scanned

↓

Bean Definition Created

↓

Constructor Called

↓

Dependency Injection

↓

@PostConstruct

↓

BeanPostProcessor

↓

Bean Ready
```

During Shutdown

```
@PreDestroy

↓

Bean Destroyed
```

Remember:

- Constructor
- DI
- @PostConstruct
- Ready
- @PreDestroy

---

# 3. IoC Container ⭐⭐⭐⭐⭐

### What is IoC?

Spring controls object creation instead of developers.

Without Spring

```java
UserService service =
new UserService();
```

With Spring

```java
@Autowired
UserService service;
```

Spring creates and injects the object.

---

# 4. Dependency Injection ⭐⭐⭐⭐⭐

Preferred

```java
Constructor Injection
```

Avoid

```java
Field Injection
```

Why?

- Easier testing
- Immutable dependencies
- Better design
- Recommended by Spring team

---

# 5. DispatcherServlet ⭐⭐⭐⭐⭐

Every request passes through it.

```
HTTP Request

↓

DispatcherServlet

↓

Handler Mapping

↓

Controller

↓

Service

↓

Repository

↓

Database

↓

JSON Response
```

Interview Definition:

Front Controller of Spring MVC.

---

# 6. Bean Scopes ⭐⭐⭐⭐☆

| Scope | Meaning |
|--------|---------|
| Singleton | One Bean (Default) |
| Prototype | New Bean Every Time |
| Request | One Per HTTP Request |
| Session | One Per Session |

Interview Tip:

95% of beans are Singleton.

---

# 7. Auto Configuration ⭐⭐⭐⭐⭐

Spring Boot automatically configures beans based on:

- Dependencies
- Classpath
- Properties

Example

Dependency:

```
spring-boot-starter-web
```

Automatically creates:

- DispatcherServlet
- Jackson
- Embedded Tomcat

No XML required.

---

# 8. @SpringBootApplication ⭐⭐⭐⭐⭐

Equivalent to

```java
@Configuration

@ComponentScan

@EnableAutoConfiguration
```

Interview Question:

What does @SpringBootApplication contain internally?

Answer:

These three annotations.

---

# 9. Circular Dependency ⭐⭐⭐⭐☆

Example

```
A

↓

B

↓

A
```

Results in:

```
BeanCurrentlyInCreationException
```

Avoid by:

- Better design
- Constructor Injection
- Splitting responsibilities

---

# 10. BeanPostProcessor ⭐⭐⭐⭐☆

Runs before and after bean initialization.

Used internally for:

- AOP
- Transactions
- Security
- Custom bean modification

Flow

```
Bean Created

↓

Before Initialization

↓

@PostConstruct

↓

After Initialization

↓

Ready
```

---

# 11. AOP ⭐⭐⭐⭐⭐

Aspect Oriented Programming separates cross-cutting concerns.

Examples

- Logging
- Transactions
- Security
- Metrics

Instead of

```
Business Logic

Logging

Security

Transactions
```

Use

```
Business Logic

↓

Aspect Handles Rest
```

---

# 12. Dynamic Proxy ⭐⭐⭐⭐⭐

Spring usually doesn't modify your class.

Instead

```
Client

↓

Proxy

↓

Actual Bean
```

Proxy intercepts calls.

Used by:

- @Transactional
- @Cacheable
- @Async
- Spring Security
- AOP

---

# 13. @Transactional Internals ⭐⭐⭐⭐⭐

Execution

```
Client

↓

Proxy

↓

Open Transaction

↓

Business Method

↓

Success?

↓

Commit

------------

Failure

↓

Rollback
```

Important

Without Proxy,

@Transactional doesn't work.

---

# 14. Why Doesn't @Transactional Work Inside Same Class?

Example

```java
public void methodA(){

    methodB();

}

@Transactional

public void methodB(){

}
```

Fails.

Reason:

```
methodA()

↓

methodB()

(No Proxy Used)
```

Proxy interception is bypassed.

This is one of the most common senior-level Spring interview questions.

---

# 15. Complete Spring Request Flow ⭐⭐⭐⭐⭐

```
Browser

↓

Tomcat

↓

Security Filter Chain

↓

DispatcherServlet

↓

Controller

↓

Service

↓

@Transactional Proxy

↓

Repository

↓

EntityManager

↓

Hibernate

↓

Database

↓

JSON Response
```

If you remember **one diagram** from Spring, remember this one.

---

# Top 20 Interview Questions

### What is IoC?

Spring manages object creation.

---

### What is Dependency Injection?

Spring injects dependencies instead of creating them manually.

---

### Constructor vs Field Injection?

Constructor Injection is preferred.

---

### What is DispatcherServlet?

Front Controller of Spring MVC.

---

### What is Auto Configuration?

Automatic bean configuration based on classpath and dependencies.

---

### What is @SpringBootApplication?

Combination of:

- @Configuration
- @ComponentScan
- @EnableAutoConfiguration

---

### Singleton vs Prototype?

Singleton → one instance.

Prototype → new instance every request from the container.

---

### Why does Spring use Proxies?

To implement:

- AOP
- Transactions
- Security
- Caching

without modifying business code.

---

### Why does @Transactional fail in self-invocation?

Because proxy interception is bypassed.

---

### How does Spring Boot start?

```
main()

↓

SpringApplication.run()

↓

ApplicationContext

↓

Bean Creation

↓

Dependency Injection

↓

Tomcat

↓

Ready
```

---

# Final Revision (5-Minute Sheet)

```
SpringApplication.run()

↓

ApplicationContext

↓

Component Scan

↓

Bean Creation

↓

Dependency Injection

↓

BeanPostProcessor

↓

Tomcat Starts
```

Request

```
Client

↓

Security Filter

↓

DispatcherServlet

↓

Controller

↓

Service

↓

@Transactional Proxy

↓

Repository

↓

EntityManager

↓

Hibernate

↓

Database
```

Remember:

✅ IoC = Spring creates objects

✅ DI = Spring injects objects

✅ DispatcherServlet = Front Controller

✅ Auto Configuration = Automatic bean setup

✅ Proxy = Transactions, Security, AOP

✅ @Transactional works through proxies

✅ Self-invocation bypasses proxy

---

# Module 7 Complete ✅

If you can explain every diagram in this file, you're well prepared for the vast majority of Spring internals questions asked in backend interviews.