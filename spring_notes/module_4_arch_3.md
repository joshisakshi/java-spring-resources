# Goal

In the previous parts, we learned:

✔ Spring Modules

✔ Core Container

✔ BeanFactory

✔ ApplicationContext

✔ BeanDefinition

Now let's connect these concepts with what actually happens when you run a Spring Boot application.

By the end of this chapter, you'll understand the complete startup flow from `main()` until your application is ready to serve requests.

---

# Table of Contents

1. Spring Boot Startup
2. Component Scanning
3. Reflection
4. Why Spring Became Popular
5. Real Project Architecture
6. Best Practices
7. Common Mistakes
8. Interview Questions
9. Cheat Sheet

---

# 1. Spring Boot Startup

Every Spring Boot application starts here.

```java
@SpringBootApplication
public class WalletApplication {

    public static void main(String[] args) {

        SpringApplication.run(
            WalletApplication.class,
            args
        );

    }

}
```

Almost everyone writes this.

Very few know what actually happens.

---

# High-Level Startup Flow

```
main()

↓

SpringApplication.run()

↓

Create ApplicationContext

↓

Prepare Environment

↓

Scan Components

↓

Create BeanDefinitions

↓

Instantiate Beans

↓

Dependency Injection

↓

Initialize Beans

↓

Embedded Tomcat Starts

↓

Application Ready
```

This entire process usually completes in a few seconds.

---

# What Does SpringApplication.run() Actually Do?

Conceptually, it performs these steps:

1. Create a SpringApplication object.
2. Determine the application type (Web, Reactive, CLI).
3. Prepare the environment.
4. Create the ApplicationContext.
5. Perform component scanning.
6. Register BeanDefinitions.
7. Instantiate singleton beans.
8. Inject dependencies.
9. Execute lifecycle callbacks.
10. Start the embedded web server (for web applications).
11. Publish startup events.
12. Return the fully initialized ApplicationContext.

> We'll revisit many of these steps in detail later.

---

# 2. Component Scanning

Interview Frequency: ⭐⭐⭐⭐⭐

Suppose you write:

```java
@Service
public class UserService {

}
```

How does Spring even know this class exists?

The answer is **Component Scanning**.

---

# Concept

Spring searches your project's packages looking for classes annotated with stereotype annotations such as:

```java
@Component

@Service

@Repository

@Controller

@RestController
```

Every discovered class becomes a candidate for bean registration.

---

# High-Level Flow

```
Project

↓

Scan Packages

↓

Find @Component

↓

Create BeanDefinition

↓

Register BeanDefinition

↓

Later Create Bean
```

Notice again:

Scanning does **not** immediately create objects.

It discovers metadata.

---

# Which Package Gets Scanned?

Suppose your structure is:

com.company.app

├── controller

├── service

├── repository

└── config

If your main class is:

```java
package com.company.app;

@SpringBootApplication
public class Application {

}
```

Spring scans:

```
com.company.app

↓

All Child Packages
```

But not sibling or parent packages.

This is why your main class is usually placed at the root package.

---

# Interview Trap

Question:

Why should the main class usually be placed in the root package?

Correct Answer:

Because component scanning starts from the package containing the main class and recursively scans its subpackages.

---

# 3. Reflection

Interview Frequency: ⭐⭐⭐⭐☆

Reflection is a Java feature that allows a program to inspect and manipulate classes at runtime.

Spring uses reflection extensively.

For example, after discovering:

```java
@Service
public class UserService {

}
```

Spring can:

- Inspect annotations.
- Discover constructors.
- Invoke constructors.
- Access methods.
- Inject fields (when needed).

Conceptually:

```
Class Metadata

↓

Reflection

↓

Create Object

↓

Inject Dependencies
```

You don't need to master the Reflection API for interviews, but you should know **Spring relies heavily on it**.

---

# Why Spring Became Popular

By now, the answer should feel natural.

Spring became popular because it:

- Simplified enterprise development.
- Promoted loose coupling.
- Encouraged POJOs.
- Improved testability.
- Reduced configuration.
- Introduced Dependency Injection.
- Was modular.
- Evolved with developer needs.

Spring Boot accelerated this by eliminating repetitive configuration.

---

# Real Project Architecture

Consider a simplified wallet service.

```
                HTTP Request

                      │

                      ▼

              WalletController

                      │

                      ▼

               WalletService

                │         │

                ▼         ▼

      WalletRepository  NotificationService

                │

                ▼

             PostgreSQL
```

Additional infrastructure:

```
JWT Security

↓

Logging (AOP)

↓

Transactions

↓

Caching

↓

Metrics
```

Even though many modules are involved, the Core Container manages the lifecycle of all these components.

---

# Best Practices

✔ Keep the main class in the root package.

✔ Organize code by feature or layer.

✔ Let Spring manage application components.

✔ Prefer constructor injection (we'll cover why later).

✔ Keep controllers thin.

✔ Put business logic in services.

✔ Put database logic in repositories.

---

# Common Mistakes

❌ Instantiating Spring-managed classes with `new`.

```java
UserService service = new UserService();
```

This bypasses the container.

---

❌ Placing the main class deep inside a package hierarchy.

Component scanning may miss other packages.

---

❌ Treating every utility class as a Spring bean.

Only classes that benefit from container management should be managed by Spring.

---

# Interview Questions

## Basic

### What is Spring Architecture?

Spring follows a modular architecture where the Core Container provides bean management and dependency injection, while higher-level modules such as MVC, Data, Security, and AOP build on top of it.

---

### Which is the most important Spring module?

The Core Container.

---

### What is Component Scanning?

The process by which Spring discovers classes annotated with stereotype annotations and registers metadata for them.

---

## Intermediate

### Does component scanning create beans?

No.

It discovers candidate classes and creates BeanDefinitions. Bean instantiation happens later during container initialization.

---

### Why should the main class be placed in the root package?

Because component scanning starts from the package of the main class and recursively scans subpackages.

---

# Revision Cheat Sheet

```
main()

↓

SpringApplication.run()

↓

Create ApplicationContext

↓

Component Scan

↓

BeanDefinitions

↓

BeanFactory

↓

Instantiate Beans

↓

Dependency Injection

↓

Initialization

↓

Embedded Tomcat

↓

Application Ready
```

---

# Module 1 Progress

✅ What is Spring

✅ Problems Before Spring

✅ History

✅ Spring Architecture

---

# Next Chapter

⭐⭐⭐⭐⭐

# 05 - Inversion of Control (IoC)

This is where Spring truly begins.

We'll answer questions like:

- What exactly is IoC?
- Why is it called "Inversion"?
- How did object creation change?
- How does Spring implement IoC?
- What is an IoC Container?
- What is controlled by the container?
- How does this improve testing and scalability?

We'll also begin introducing actual Spring classes such as:

- BeanFactory
- DefaultListableBeanFactory
- AbstractApplicationContext

to understand how the framework implements IoC internally.