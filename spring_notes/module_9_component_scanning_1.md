# Module 1 - Spring Fundamentals

# Chapter 09 - Component Scanning & Stereotype Annotations

## Part 1 — How Spring Discovers Your Beans

---

# Interview Frequency

⭐⭐⭐⭐⭐ (Extremely High)

Asked In:

- Amazon
- Microsoft
- Walmart
- Goldman Sachs
- JP Morgan
- Atlassian
- Visa
- Oracle
- Almost every Spring interview

---

# Goal

By the end of this chapter you should understand:

- How Spring finds your classes
- What Component Scanning is
- What Bean Discovery means
- What is a BeanDefinition
- Why packages matter
- What @ComponentScan actually does
- Internal startup sequence

---

# Table of Contents

1. The Problem
2. Bean Discovery
3. Component Scanning
4. Package Scanning
5. BeanDefinition
6. Startup Flow
7. Internal Working
8. Best Practices
9. Interview Questions

---

# 1. The Problem

Suppose you write:

```java
@Service
public class PaymentService {

}
```

Question:

How does Spring even know this class exists?

Nobody writes:

```java
new PaymentService();
```

Nobody registers it manually.

Yet Spring creates the bean.

How?

---

# Before Spring

Imagine a project with

```
300 classes
```

You had to manually create every object.

```
Main

↓

new UserService()

↓

new PaymentService()

↓

new InventoryService()

↓

new EmailService()
```

Everything was manual.

---

# With Spring

You simply write

```java
@Service
public class PaymentService {

}
```

Spring automatically discovers it.

```
Application Starts

↓

Scan Packages

↓

Found PaymentService

↓

Create Bean
```

This automatic discovery is called

# Component Scanning

---

# 2. Bean Discovery

Bean Discovery is the process by which Spring finds candidate classes that should become beans.

Think of it like Google searching your project.

```
Project

↓

Scan Packages

↓

Look At Every Class

↓

Check Annotations

↓

Eligible?

↓

Create BeanDefinition
```

Notice something.

Spring doesn't create objects immediately.

First,

it discovers candidates.

---

# Bean Discovery vs Bean Creation

Interviewers love this distinction.

```
Discovery

↓

Find Classes

----------------

Creation

↓

Instantiate Objects
```

These are two different phases.

---

# 3. Component Scanning

Definition

Component Scanning is Spring's mechanism for automatically finding classes annotated with stereotype annotations and registering them as bean definitions.

---

# Example

```java
@Component
public class EmailService {

}
```

When Spring scans the package,

it sees

```
@Component
```

and marks the class as a bean candidate.

---

# Another Example

```java
@Service
public class UserService {

}
```

Same process.

```
Scan Class

↓

@Service Found

↓

Register BeanDefinition
```

---

# What Gets Scanned?

By default,

Spring scans classes annotated with:

```
@Component

@Service

@Repository

@Controller

@RestController

@Configuration
```

We'll study each annotation in the next part.

---

# 4. Package Scanning

Consider this structure.

```
com.company

│

├── Application.java

│

├── controller

│

├── service

│

├── repository

│

└── config
```

Application starts from

```java
@SpringBootApplication
public class Application {

}
```

Spring begins scanning from this package.

```
com.company

↓

controller

↓

service

↓

repository

↓

config
```

Everything underneath is scanned recursively.

---

# What If a Package Is Outside?

Suppose

```
com.company

↓

Application.java
```

but

```
com.external.payment

↓

PaymentService
```

Spring never reaches it.

Therefore,

```
No Bean
```

This is one of the most common beginner mistakes.

---

# How To Fix It?

Option 1 (Preferred)

Move the package under the application's root package.

Example:

```
com.company

│

├── Application

├── service

├── payment

├── controller
```

---

Option 2

Use

```java
@ComponentScan
```

```java
@ComponentScan(
    basePackages = {
        "com.company",
        "com.external.payment"
    }
)
```

Now both packages are scanned.

---

# 5. BeanDefinition

Interview Frequency

⭐⭐⭐⭐⭐

This is where Spring becomes interesting.

Suppose Spring discovers

```java
@Service
public class UserService {

}
```

Does Spring immediately create

```
new UserService()
```

?

No.

Instead,

it creates metadata.

This metadata is called

# BeanDefinition

---

# What Is BeanDefinition?

Think of BeanDefinition as Spring's blueprint for creating a bean.

It contains information such as:

- Bean class
- Scope
- Lazy or eager initialization
- Constructor information
- Lifecycle methods
- Bean name

Conceptually:

```
BeanDefinition

↓

Class

↓

Scope

↓

Constructor

↓

Metadata
```

Only later does Spring use this metadata to create the actual object.

---

# 6. Startup Flow

Putting everything together:

```
Application Starts

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

Inject Dependencies

↓

Initialize Beans

↓

Application Ready
```

Notice again:

**BeanDefinition comes before Bean creation.**

This is a recurring interview point.

---

# 7. Internal Execution

Conceptually,

Spring performs something like:

```
ApplicationContext

↓

ClassPath Scanner

↓

Read Class Metadata

↓

@Component?

↓

Yes

↓

Create BeanDefinition

↓

Register

↓

Continue Scanning
```

Later,

during bean creation,

Spring reads these BeanDefinitions.

---

# Internal Architecture

```
                Project Classes

                       │

                       ▼

            Component Scanner

                       │

                       ▼

             Read Annotations

                       │

                       ▼

              BeanDefinition

                       │

                       ▼

       BeanDefinition Registry

                       │

                       ▼

         BeanFactory / Container

                       │

                       ▼

             Bean Instantiation
```

This is a very good interview diagram.

---

# Real Project Example

Suppose you're building a staking backend.

Project:

```
controller

↓

ChainController

service

↓

ChainService

repository

↓

ChainRepository

config

↓

RedisConfig
```

Startup:

```
Scan Packages

↓

Find Classes

↓

Create BeanDefinitions

↓

Register

↓

Instantiate Beans

↓

Inject Dependencies

↓

Application Ready
```

---

# Best Practices

✅ Keep the main application class in the root package.

✅ Organize packages logically.

✅ Avoid unnecessary custom component scans.

✅ Understand that scanning happens once during startup.

---

# Interview Perspective

## Question

What is Component Scanning?

Excellent Answer:

> Component Scanning is Spring's mechanism for automatically discovering classes annotated with stereotype annotations such as `@Component`, `@Service`, `@Repository`, and `@Controller`. Instead of immediately creating objects, Spring first creates BeanDefinitions and later instantiates the corresponding beans during container initialization.

---

## Question

What is a BeanDefinition?

Excellent Answer:

> A BeanDefinition is Spring's internal metadata representation of a bean. It stores information such as the bean class, scope, initialization methods, and lifecycle configuration, allowing the IoC container to instantiate and manage the bean later.

---

# Common Interview Traps

### Trap 1

Does Component Scanning immediately create beans?

❌ No.

It creates BeanDefinitions first.

---

### Trap 2

Does Spring scan the entire JVM?

❌ No.

It scans only the configured base packages.

---

### Trap 3

Can a class outside the scanned packages become a bean automatically?

❌ No.

It must either be scanned or explicitly registered using `@Bean`.

---

# Summary

Remember:

- Component Scanning discovers candidate classes.
- Bean Discovery happens before Bean Creation.
- Spring first creates BeanDefinitions.
- BeanDefinitions contain metadata, not objects.
- The Application class location determines the default scanning root.

---

# Revision Cheat Sheet

```
Application Starts

↓

Component Scan

↓

Find @Component

↓

BeanDefinition

↓

Register

↓

Create Bean

↓

Dependency Injection

↓

Initialize

↓

Ready
```

---

# Exercises

1. Explain Component Scanning.
2. What is the difference between Bean Discovery and Bean Creation?
3. What is a BeanDefinition?
4. Why should the main application class be placed in the root package?
5. What happens if a package is not scanned?

---

## End of Part 1

# Next Part

We'll study the stereotype annotations in depth:

- `@Component`
- `@Service`
- `@Repository`
- `@Controller`
- `@RestController`
- `@Configuration`

We'll answer:

- Are they functionally different?
- Why do all of them exist?
- Which one should you use?
- How does Spring treat each annotation internally?
- Which ones are commonly asked in interviews?