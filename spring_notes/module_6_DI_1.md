# Module 1 - Spring Fundamentals

# Chapter 06 - Dependency Injection (DI)

## Part 1 — Understanding Dependency Injection

---

# Interview Frequency

⭐⭐⭐⭐⭐ (Extremely High)

Asked In:

- Amazon
- Walmart Global Tech
- Atlassian
- Goldman Sachs
- JP Morgan
- Visa
- Microsoft
- Oracle
- PhonePe
- Razorpay
- Almost every Spring Boot interview

> **Interview Insight**
>
> If there is one Spring topic that interviewers almost always ask after IoC, it is **Dependency Injection**.
>
> A typical interview flow is:
>
> - What is IoC?
> - How does Spring implement IoC?
> - What is Dependency Injection?
> - Why constructor injection?
> - How does `@Autowired` work internally?

Mastering this chapter alone can answer a significant portion of Spring interview questions.

---

# Goal

By the end of this chapter, you should be able to answer:

- What is a dependency?
- What is Dependency Injection?
- Why was it introduced?
- How is it related to IoC?
- How does it reduce coupling?
- What problems does it solve?
- How is it used in real Spring Boot applications?

---

# Table of Contents

1. What is a Dependency?
2. Why Dependencies Become a Problem
3. What is Dependency Injection?
4. Relationship Between IoC and DI
5. Before vs After DI
6. Benefits
7. Real Project Example
8. Interview Questions
9. Summary
10. Cheat Sheet

---

# 1. What is a Dependency?

Before learning Dependency Injection, let's understand the word **dependency**.

Suppose we have:

```java
public class UserService {

    private EmailService emailService;

}
```

Question:

Does `UserService` work without `EmailService`?

No.

It depends on `EmailService`.

Therefore,

```
EmailService

↓

Dependency

↓

Required By

↓

UserService
```

A dependency is simply **another object that a class needs to perform its work.**

---

# More Examples

```
OrderService

↓

PaymentService
```

PaymentService is a dependency.

---

```
WalletService

↓

WalletRepository
```

WalletRepository is a dependency.

---

```
NotificationService

↓

SmsGateway
```

SmsGateway is a dependency.

---

Almost every class in an enterprise application depends on other classes.

---

# Visualizing Dependencies

Imagine a backend service.

```
                    UserController

                           │

                           ▼

                    UserService

                     /       \

                    ▼         ▼

         UserRepository   EmailService

                    │

                    ▼

                PostgreSQL
```

Every arrow represents a dependency.

The larger the project,

the more dependencies exist.

---

# 2. Why Do Dependencies Become a Problem?

Let's create an OrderService.

```java
public class OrderService {

    private PaymentService paymentService =
            new PaymentService();

}
```

Looks fine.

Now imagine PaymentService changes.

Old constructor:

```java
PaymentService()
```

New constructor:

```java
PaymentService(
        Database database,
        Logger logger,
        Cache cache
)
```

Now every place that creates PaymentService must also change.

This creates maintenance problems.

---

# Tight Coupling

Consider:

```java
private PaymentService paymentService =
        new PaymentService();
```

OrderService now knows:

- Which implementation to use.
- How to create it.
- When to create it.
- How long it lives.

That's too much responsibility.

This is called **tight coupling**.

---

# Why Tight Coupling Is Bad

Imagine tomorrow your company changes:

```
StripePaymentService

↓

RazorpayPaymentService
```

Without DI:

Every class using:

```java
new StripePaymentService()
```

must be modified.

With hundreds of services,

this becomes painful.

---

# 3. What is Dependency Injection?

## Interview Definition

> Dependency Injection is a design pattern in which the dependencies required by a class are provided from the outside instead of the class creating them itself.

Read that again.

The class does **not** create its dependencies.

Someone else provides them.

In Spring,

that "someone else" is the **IoC Container**.

---

# Before Dependency Injection

```java
public class UserService {

    private EmailService emailService =
            new EmailService();

}
```

Who creates EmailService?

UserService itself.

---

# After Dependency Injection

```java
@Service
public class UserService {

    private final EmailService emailService;

    public UserService(EmailService emailService) {

        this.emailService = emailService;

    }

}
```

Notice something.

There is no:

```java
new EmailService();
```

UserService simply says:

> "I need an EmailService."

Spring provides it.

---

# Key Idea

Dependency Injection is based on one simple principle:

> **A class should declare what it needs, not create what it needs.**

That single sentence captures the essence of DI.

---

# 4. Relationship Between IoC and DI

This is one of the most common interview questions.

Many developers say:

> IoC and DI are the same.

They are not.

```
               IoC

        (Design Principle)

               │

               ▼

Dependency Injection

 (Implementation Technique)

               │

               ▼

Spring Framework
```

Think of IoC as the **goal**.

Think of DI as the **mechanism** Spring uses to achieve that goal.

---

# Analogy

Suppose you join a company.

IoC says:

> Employees shouldn't buy and assemble their own laptops.

Dependency Injection says:

> The IT department gives every employee a configured laptop.

The company controls resources.

Employees focus on work.

Spring follows the same philosophy.

---

# 5. Before vs After DI

## Without Dependency Injection

```
Application

↓

new UserService()

↓

new EmailService()

↓

new Database()

↓

new Logger()
```

The application manages everything.

---

## With Dependency Injection

```
Application

↓

Need UserService

↓

Spring Container

↓

Create EmailService

↓

Create UserService

↓

Inject EmailService

↓

Return Ready Object
```

The application focuses only on business logic.

---

# Code Comparison

## Without DI

```java
public class NotificationService {

    private EmailService emailService =
            new EmailService();

}
```

---

## With DI

```java
@Service
public class NotificationService {

    private final EmailService emailService;

    public NotificationService(EmailService emailService) {

        this.emailService = emailService;

    }

}
```

Cleaner.

More maintainable.

Easier to test.

---

# 6. Benefits of Dependency Injection

## Loose Coupling

Classes depend on abstractions rather than creating implementations.

---

## Better Testing

Suppose:

```java
UserService
```

depends on

```java
EmailService
```

During testing,

instead of injecting the real EmailService,

you can inject:

```java
MockEmailService
```

No production emails are sent.

This is exactly how Mockito works.

---

## Easier Maintenance

If EmailService changes internally,

UserService usually remains unchanged.

Only the injected implementation changes.

---

## Better Readability

Constructor parameters immediately tell us what a class depends on.

Example:

```java
public OrderService(
        PaymentService paymentService,
        InventoryService inventoryService,
        NotificationService notificationService
)
```

Even before reading the code,

we understand the dependencies.

---

## Centralized Object Management

Instead of every class managing objects,

Spring manages them centrally.

This reduces duplicate object creation.

---

# Real Project Example

Let's use a fintech staking onboarding service.

```
ChainController

↓

ChainService

↓

ValidatorService

↓

ChainRepository
```

Dependencies:

```
ChainController

↓

ChainService
```

```
ChainService

↓

ChainRepository
```

```
ChainService

↓

ValidatorService
```

None of these classes instantiate each other.

Instead,

Spring injects the required dependencies during application startup.

This allows each class to focus on one responsibility.

---

# Interview Perspective

⭐⭐⭐⭐⭐

## Question

What is Dependency Injection?

### Weak Answer

Spring injects objects.

---

### Good Answer

Dependency Injection is a design pattern where dependencies are provided externally instead of being created by the class itself.

---

### Excellent Answer

Dependency Injection is Spring's primary mechanism for implementing Inversion of Control. Instead of classes instantiating their own dependencies, they declare what they require, and the IoC container resolves, creates, and injects the appropriate beans. This results in loose coupling, better testability, and easier maintenance.

---

# Common Interview Traps

---

## Trap 1

**Question**

Is Dependency Injection a Spring feature?

**Answer**

No.

Dependency Injection is a design pattern.

Spring is one framework that implements it.

---

## Trap 2

**Question**

Does Dependency Injection create objects?

**Answer**

No.

The IoC Container creates objects.

Dependency Injection is the process of supplying those objects to dependent classes.

---

## Trap 3

**Question**

Can we use Dependency Injection without Spring?

**Answer**

Yes.

You can manually inject dependencies in plain Java.

Spring automates and manages the process.

---

# Summary

Remember:

- A dependency is any object another class requires.
- Dependency Injection means dependencies are provided externally.
- Spring uses DI to implement IoC.
- DI reduces tight coupling.
- DI improves maintainability, readability, and testability.

---

# Revision Cheat Sheet

```
Class

↓

Needs Dependency

↓

Declares Dependency

↓

Spring Container

↓

Creates Dependency

↓

Injects Dependency

↓

Ready Bean
```

---

# Exercises

## Conceptual

1. What is a dependency?
2. Why is `new` considered tight coupling in enterprise applications?
3. Explain Dependency Injection in your own words.
4. Differentiate IoC and Dependency Injection.

---

## Interview Practice

**Question:**

Why is Dependency Injection better than creating objects using the `new` keyword?

Try answering in under 90 seconds.

Your answer should mention:

- Loose coupling
- Testability
- Maintainability
- Separation of responsibilities

---

## End of Part 1

# Next Part

Now we move from **concepts** to **implementation**.

We'll answer:

- What is `@Autowired`?
- How does Spring decide which bean to inject?
- What happens internally when Spring sees `@Autowired`?
- What is `AutowiredAnnotationBeanPostProcessor`?
- What is dependency resolution?
- What happens if two beans of the same type exist?

> ⭐⭐⭐⭐⭐ This is one of the most frequently asked Spring interview topics and is where we'll begin exploring the internals of dependency resolution.