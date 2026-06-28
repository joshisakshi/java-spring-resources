# Module 1 - Spring Fundamentals

# Chapter 07 - Bean & Bean Lifecycle

## Part 2 — Lifecycle Callbacks (`@PostConstruct`, `@PreDestroy`, BeanPostProcessor & Initialization Order)

---

# Interview Frequency

⭐⭐⭐⭐⭐ (Extremely High)

Asked In:

- Amazon
- Microsoft
- Walmart
- Goldman Sachs
- JP Morgan
- Visa
- Atlassian
- Product Companies

---

# Goal

By the end of this chapter you should understand:

- Every lifecycle callback
- Exact execution order
- @PostConstruct
- @PreDestroy
- InitializingBean
- DisposableBean
- initMethod
- destroyMethod
- BeanPostProcessor
- Which callback should be used in production
- How Spring modifies beans internally

---

# Table of Contents

1. Why Lifecycle Callbacks Exist
2. Complete Execution Order
3. @PostConstruct
4. @PreDestroy
5. InitializingBean
6. DisposableBean
7. initMethod & destroyMethod
8. BeanPostProcessor
9. Complete Timeline
10. Best Practices
11. Interview Questions

---

# 1. Why Lifecycle Callbacks Exist?

Imagine you have a service that connects to PostgreSQL.

```java
@Service
public class DatabaseService {

}
```

Immediately after Spring creates this object you may want to:

- Open a database connection
- Load configuration
- Initialize cache
- Read API keys
- Warm up expensive resources

Similarly, before the application shuts down you may want to:

- Close database connections
- Flush logs
- Release file handles
- Close sockets

Java constructors are **not** always the best place for this.

Why?

Because when the constructor runs,

dependency injection has **not completed yet**.

Spring therefore provides lifecycle callbacks.

---

# 2. Complete Bean Lifecycle Execution Order

This is one of the most important diagrams in Spring.

```
Component Scan

↓

BeanDefinition

↓

Instantiate Bean

↓

Dependency Injection

↓

Aware Interfaces

↓

BeanPostProcessor
(before initialization)

↓

@PostConstruct

↓

afterPropertiesSet()

↓

Custom initMethod()

↓

BeanPostProcessor
(after initialization)

↓

Bean Ready

↓

Application Running

↓

@PreDestroy

↓

destroy()

↓

Custom destroyMethod()
```

You do **not** need to memorize every line,

but you should understand the order.

---

# 3. @PostConstruct

Interview Frequency

⭐⭐⭐⭐⭐

Definition:

`@PostConstruct` marks a method that Spring calls **after dependency injection is complete but before the bean is available for use.**

Example:

```java
@Service
public class EmailService {

    @PostConstruct
    public void init() {

        System.out.println("Initializing Email Service");

    }

}
```

Execution:

```
Create Object

↓

Inject Dependencies

↓

@PostConstruct

↓

Bean Ready
```

---

# Why Not Constructor?

Constructor:

```java
public EmailService() {

}
```

runs before dependency injection.

Suppose:

```java
@Autowired

private ConfigService configService;
```

Inside the constructor,

`configService` may not yet be usable.

With `@PostConstruct`,

all dependencies have already been injected.

---

# Real Project Usage

Common use cases:

✅ Loading ML models

✅ Reading configuration

✅ Creating connection pools

✅ Loading Redis cache

✅ Initializing thread pools

---

# 4. @PreDestroy

Interview Frequency

⭐⭐⭐⭐⭐

Definition:

`@PreDestroy` marks a method that Spring invokes just before destroying the bean.

Example:

```java
@Service
public class EmailService {

    @PreDestroy
    public void cleanup() {

        System.out.println("Closing resources");

    }

}
```

Execution:

```
Application Shutdown

↓

@PreDestroy

↓

Bean Destroyed
```

---

# Common Use Cases

- Close sockets
- Close database connections
- Stop worker threads
- Release native resources
- Flush buffers

---

# Important Interview Point

`@PreDestroy`

is only called for beans whose lifecycle Spring manages.

If you create:

```java
new EmailService();
```

Spring knows nothing about it.

No callback executes.

---

# 5. InitializingBean

Spring also provides an interface.

```java
public class UserService
implements InitializingBean {

    @Override

    public void afterPropertiesSet() {

    }

}
```

Spring automatically calls:

```
afterPropertiesSet()
```

after dependency injection.

---

# Why Is It Less Popular?

It tightly couples your code to Spring.

Your business class now imports:

```java
org.springframework.beans
```

Generally,

annotation-based callbacks are cleaner.

---

# 6. DisposableBean

Similarly,

Spring provides:

```java
implements DisposableBean
```

Example:

```java
public class UserService
implements DisposableBean {

    @Override

    public void destroy() {

    }

}
```

Called during application shutdown.

Again,

it couples your code to Spring.

---

# 7. initMethod & destroyMethod

Instead of annotations,

you can configure callbacks explicitly.

Example:

```java
@Bean(
    initMethod = "initialize",
    destroyMethod = "cleanup"
)
public CacheService cacheService() {

    return new CacheService();

}
```

Spring calls:

```
initialize()

↓

Application Running

↓

cleanup()
```

This is useful when:

- Using third-party libraries
- You cannot modify the source code
- Legacy systems

---

# 8. BeanPostProcessor

⭐⭐⭐⭐⭐

This is where Spring becomes really powerful.

Definition:

A BeanPostProcessor allows Spring to intercept every bean before and after initialization.

Think of it as middleware for beans.

```
Bean Created

↓

BeanPostProcessor

↓

Bean Initialized
```

---

# Why Does Spring Need It?

Many famous Spring features use BeanPostProcessor.

Examples:

- @Autowired
- @Transactional
- Spring Security
- AOP
- @Async
- @EventListener

None of these are implemented directly by the annotations.

They are implemented using BeanPostProcessors (or related post-processing infrastructure).

---

# Internal Flow

Conceptually,

Spring does something like:

```
Create Bean

↓

Inject Dependencies

↓

Run BeanPostProcessor

↓

Apply Proxy?

↓

Initialize Bean

↓

Run BeanPostProcessor

↓

Bean Ready
```

---

# Example BeanPostProcessor

```java
@Component
public class LoggingProcessor
implements BeanPostProcessor {

    @Override

    public Object postProcessBeforeInitialization(
            Object bean,
            String beanName) {

        System.out.println(beanName);

        return bean;

    }

}
```

This processor executes for **every bean**.

---

# Complete Timeline

Let's combine everything.

```
Application Starts

↓

Component Scan

↓

BeanDefinition Created

↓

Object Instantiated

↓

Dependency Injection

↓

Aware Interfaces

↓

BeanPostProcessor
(Before)

↓

@PostConstruct

↓

afterPropertiesSet()

↓

initMethod()

↓

BeanPostProcessor
(After)

↓

Bean Ready

↓

Application Running

↓

@PreDestroy

↓

destroy()

↓

destroyMethod()
```

This is the lifecycle you should remember.

---

# Which Callback Should You Use?

| Mechanism | Recommended |
|-----------|------------|
| Constructor | Only lightweight initialization |
| @PostConstruct | ✅ Preferred |
| InitializingBean | Sometimes |
| initMethod | Good for third-party classes |
| @PreDestroy | ✅ Preferred |
| DisposableBean | Sometimes |

---

# Real Project Example

Imagine you're building a staking backend.

```
ValidatorService

↓

@PostConstruct

↓

Load Supported Chains

↓

Initialize Validator Cache

↓

Application Running

↓

@PreDestroy

↓

Persist Metrics

↓

Close Resources
```

This avoids expensive initialization during the first request.

---

# Interview Perspective

## Question

When does `@PostConstruct` execute?

Excellent Answer:

> After the bean has been instantiated and all dependencies have been injected, but before the bean is made available for regular use.

---

## Question

Why not initialize everything inside the constructor?

Excellent Answer:

> Constructors execute before dependency injection is complete. If initialization depends on injected beans, `@PostConstruct` is the appropriate lifecycle callback.

---

## Question

How does Spring implement features like `@Transactional`?

Excellent Answer:

> Spring uses infrastructure such as BeanPostProcessors to inspect beans during initialization. It may wrap eligible beans with proxies, allowing features like transactions, security, and AOP to be applied transparently.

---

# Common Interview Traps

### Trap 1

Does `@PostConstruct` execute before dependency injection?

❌ No.

It executes **after** dependency injection.

---

### Trap 2

Can `@PreDestroy` execute on objects created with `new`?

❌ No.

Only Spring-managed beans participate in the lifecycle.

---

### Trap 3

Is BeanPostProcessor only used for custom processors?

❌ No.

Spring itself relies heavily on BeanPostProcessors to implement many core framework features.

---

# Best Practices

✅ Keep constructors lightweight.

✅ Use `@PostConstruct` for initialization that depends on injected beans.

✅ Use `@PreDestroy` for cleanup.

✅ Avoid implementing Spring interfaces (`InitializingBean`, `DisposableBean`) unless there is a specific reason.

✅ Never perform heavy work inside constructors.

---

# Summary

Remember:

- Constructors create the object.
- Dependency Injection happens next.
- `@PostConstruct` runs after injection.
- BeanPostProcessors can modify or wrap beans.
- `@PreDestroy` executes during shutdown.
- Bean lifecycle management is one of Spring's biggest advantages over plain Java.

---

# Revision Cheat Sheet

```
Constructor

↓

Dependency Injection

↓

BeanPostProcessor (Before)

↓

@PostConstruct

↓

afterPropertiesSet()

↓

initMethod()

↓

BeanPostProcessor (After)

↓

Bean Ready

↓

@PreDestroy

↓

destroy()

↓

destroyMethod()
```

---

# Exercises

1. Explain the complete Bean Lifecycle in order.
2. Why is `@PostConstruct` preferred over constructor initialization for injected dependencies?
3. What is a BeanPostProcessor?
4. Name three Spring features that rely on BeanPostProcessors.
5. Explain when `@PreDestroy` is executed.

---

## End of Part 2

# Next Part

We'll move to **Bean Scopes**, where we'll answer:

- Why does Spring create only one instance of a service by default?
- What is Singleton scope?
- What is Prototype scope?
- Request, Session, and Application scopes
- Thread safety implications
- Memory and performance trade-offs
- Why Singleton is the default in Spring
- Common interview scenarios involving bean scopes