# Module 1 - Spring Fundamentals

# Chapter 06 - Dependency Injection (DI)

## Part 3 — Multiple Beans, @Qualifier, @Primary & Circular Dependencies

---

# Interview Frequency

⭐⭐⭐⭐⭐

One of the most frequently asked follow-up topics after `@Autowired`.

Common interview questions:

- What if two beans of the same type exist?
- How does Spring decide which bean to inject?
- What is `@Qualifier`?
- What is `@Primary`?
- What are Circular Dependencies?
- Why does Constructor Injection fail with circular dependencies?

---

# Goal

By the end of this chapter you should understand:

- Bean ambiguity
- How Spring resolves beans
- @Qualifier
- @Primary
- Dependency resolution priority
- Circular dependencies
- Constructor vs Field injection in circular dependencies

---

# Table of Contents

1. Bean Ambiguity
2. @Primary
3. @Qualifier
4. Dependency Resolution Algorithm
5. Circular Dependencies
6. Constructor Circular Dependency
7. Field Injection Circular Dependency
8. Best Practices
9. Interview Questions

---

# 1. Bean Ambiguity

Imagine this interface.

```java
public interface NotificationService {

    void send();

}
```

Two implementations.

```java
@Service
public class EmailNotificationService
        implements NotificationService {

}
```

```java
@Service
public class SmsNotificationService
        implements NotificationService {

}
```

Now another class.

```java
@Service
public class UserService {

    @Autowired

    private NotificationService notificationService;

}
```

Question:

Which implementation should Spring inject?

```
NotificationService

↓

Found

↓

EmailNotificationService

SmsNotificationService
```

Spring has a problem.

There are two valid candidates.

This is called

# Bean Ambiguity

---

# Startup Failure

Spring refuses to guess.

Instead,

it throws:

```
NoUniqueBeanDefinitionException
```

Interviewers LOVE asking this.

Remember:

Spring prefers failing fast over making incorrect assumptions.

---

# 2. @Primary

Suppose your application mostly uses Email.

Only occasionally uses SMS.

You can declare one implementation as the default.

```java
@Service

@Primary

public class EmailNotificationService
        implements NotificationService {

}
```

Now Spring sees:

```
Need NotificationService

↓

EmailNotificationService

(Primary)

↓

Inject
```

If multiple candidates exist,

Spring chooses the bean marked with

```
@Primary
```

---

# When Should You Use @Primary?

Good use cases:

✔ Default implementation

✔ Most commonly used bean

✔ Global preference

Examples:

```
PaymentGateway

↓

StripeGateway (Primary)

↓

PayPalGateway
```

Most requests use Stripe.

A few use PayPal explicitly.

---

# 3. @Qualifier

Suppose you need SMS.

Even though Email is Primary.

```java
@Autowired

@Qualifier("smsNotificationService")

private NotificationService service;
```

Now Spring ignores Primary.

Instead,

it injects:

```
smsNotificationService
```

---

# Internal Resolution

```
Need NotificationService

↓

Multiple Candidates

↓

Qualifier Present?

↓

Yes

↓

Inject Matching Bean
```

---

# @Primary vs @Qualifier

This comparison is asked constantly.

| Feature | @Primary | @Qualifier |
|----------|-----------|------------|
| Purpose | Default Bean | Specific Bean |
| Scope | Global | Local Injection Point |
| Overrides | No | Yes |
| Typical Use | Preferred implementation | Explicit selection |

---

# Interview Tip

If both exist:

```java
@Primary
```

and

```java
@Qualifier
```

Who wins?

Answer:

```
@Qualifier
```

Always.

Because the developer explicitly requested that bean.

---

# Dependency Resolution Priority

Spring follows this order.

```
Need Bean

↓

Exactly One Bean?

↓

Yes

↓

Inject

---------------

No

↓

Qualifier Present?

↓

Yes

↓

Inject Matching Bean

---------------

No

↓

Primary Bean Exists?

↓

Yes

↓

Inject Primary

---------------

No

↓

Throw Exception
```

This flow is worth remembering.

---

# Internal Execution

Conceptually,

Spring performs something similar to:

```
resolveDependency()

↓

Find Candidates

↓

Filter

↓

Qualifier?

↓

Primary?

↓

Inject

↓

Done
```

The real implementation is more complex,

but this mental model is sufficient for interviews.

---

# 5. Circular Dependencies

This is one of Spring's most famous interview topics.

Imagine:

```java
@Service
class A {

}
```

and

```java
@Service
class B {

}
```

Now:

```java
@Service

class A {

    private final B b;

}
```

and

```java
@Service

class B {

    private final A a;

}
```

Look carefully.

```
Create A

↓

Need B

↓

Create B

↓

Need A

↓

Create A

↓

Need B

↓

Need A

↓

Need B

↓

Infinite Loop
```

This is called

# Circular Dependency

---

# Constructor Injection Circular Dependency

Example:

```java
@Service

class A {

    public A(B b){

    }

}
```

```java
@Service

class B {

    public B(A a){

    }

}
```

Application startup fails.

Reason:

Neither object can be created first.

---

# Why Can't Spring Solve This?

Suppose Spring starts with A.

```
Create A

↓

Need B

↓

Create B

↓

Need A

↓

A Doesn't Exist Yet
```

Deadlock.

No object can be completed.

---

# Error

Typically you'll see:

```
BeanCurrentlyInCreationException
```

or a message indicating a circular dependency.

---

# Field Injection Example

Historically,

Spring could sometimes resolve circular dependencies involving field or setter injection by exposing an early reference to a partially constructed singleton.

Example:

```java
@Service
class A {

    @Autowired
    private B b;

}
```

```java
@Service
class B {

    @Autowired
    private A a;

}
```

Conceptually:

```
Create A

↓

Register Early Reference

↓

Need B

↓

Create B

↓

Inject Early A

↓

Finish B

↓

Finish A
```

**Important Interview Note**

Modern Spring Boot versions discourage circular dependencies, and by default many circular references are no longer allowed. The best practice is **not to rely on Spring resolving them**.

Instead, redesign the classes to remove the cycle.

---

# How To Fix Circular Dependencies

Usually,

a circular dependency indicates poor design.

Common fixes:

- Extract shared logic into another service.
- Depend on an abstraction rather than a concrete class.
- Re-evaluate class responsibilities.
- Use events or messaging if appropriate.

Avoid using `@Lazy` as the first solution—it can break the cycle, but it often hides a design problem rather than solving it.

---

# Real Project Example

Suppose you're building a payment system.

Bad design:

```
PaymentService

↓

NotificationService

↓

PaymentService
```

Better design:

```
PaymentService

↓

PaymentEventPublisher

↓

NotificationService
```

Now:

PaymentService doesn't directly depend on NotificationService.

The cycle disappears.

---

# Interview Perspective

⭐⭐⭐⭐⭐

## Question

How does Spring choose which bean to inject?

Excellent Answer:

> Spring first searches for beans of the required type. If exactly one bean exists, it injects that bean. If multiple candidates exist, it checks for a matching `@Qualifier`. If no qualifier is provided, it looks for a bean marked with `@Primary`. If ambiguity still remains, Spring throws a `NoUniqueBeanDefinitionException` rather than making an arbitrary choice.

---

## Question

What is the difference between `@Primary` and `@Qualifier`?

Excellent Answer:

> `@Primary` declares the default bean for a type across the application, whereas `@Qualifier` selects a specific bean at a particular injection point. If both are present, `@Qualifier` takes precedence because it is an explicit developer instruction.

---

## Question

Why is circular dependency considered bad?

Excellent Answer:

> It indicates tight coupling between components, makes the dependency graph difficult to understand, complicates testing, and can prevent successful bean creation. It's generally a design smell that should be refactored rather than worked around.

---

# Common Interview Traps

### Trap 1

Does Spring randomly choose one bean?

❌ No.

It throws an exception if it cannot determine a unique candidate.

---

### Trap 2

Does `@Primary` always win?

❌ No.

A matching `@Qualifier` overrides `@Primary`.

---

### Trap 3

Should we use `@Lazy` to fix circular dependencies?

❌ Not as the default approach.

Refactoring the design is preferred.

---

# Best Practices

✅ Prefer constructor injection.

✅ Keep dependencies acyclic.

✅ Use `@Qualifier` only when multiple implementations are genuinely needed.

✅ Use `@Primary` for a sensible application-wide default.

✅ Depend on interfaces, not concrete implementations.

---

# Summary

Remember:

- Multiple beans of the same type create ambiguity.
- `@Primary` provides a default implementation.
- `@Qualifier` selects a specific implementation.
- `@Qualifier` overrides `@Primary`.
- Circular dependencies are a design smell.
- Constructor injection exposes circular dependencies early, which is generally beneficial.

---

# Revision Cheat Sheet

```
Need Bean

↓

One Candidate?

↓

Yes → Inject

↓

No

↓

Qualifier?

↓

Yes → Inject

↓

No

↓

Primary?

↓

Yes → Inject

↓

No

↓

NoUniqueBeanDefinitionException
```

---

## End of Part 3

# Next Chapter

⭐⭐⭐⭐⭐ **Bean Lifecycle** (One of the most important chapters in Spring)

We'll answer:

- What exactly is a Bean?
- How is a Bean different from a Java object?
- How are Beans created?
- What are Bean Lifecycle callbacks?
- What are `@PostConstruct` and `@PreDestroy`?
- What is `BeanPostProcessor`?
- What is `InitializingBean`?
- What is `DisposableBean`?
- How does Spring initialize thousands of beans during startup?

This chapter connects everything we've learned so far—IoC, DI, and the container—into a complete picture of how Spring manages application objects.