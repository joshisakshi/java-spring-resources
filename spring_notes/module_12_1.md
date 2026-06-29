# Spring Backend Interview Handbook

# Module 2 — Spring Boot

> **Interview Importance:** ⭐⭐⭐⭐⭐
>
> **Target Companies:** Amazon, Google, Microsoft, Atlassian, Walmart Global Tech, Goldman Sachs, JP Morgan, Visa, Oracle, Adobe, Intuit, Razorpay, PhonePe, CRED and other product-based/backend companies.

---

# Goal of This Module

After completing this module, you should be able to answer confidently:

- Why was Spring Boot introduced?
- How does Spring Boot reduce boilerplate?
- What exactly happens inside `SpringApplication.run()`?
- What is `@SpringBootApplication` internally?
- How does Auto Configuration work?
- What are Starter Dependencies?
- How are configuration files loaded?
- How do Profiles work?
- What is `@ConfigurationProperties`?
- How does Embedded Tomcat start?
- What is Spring Boot Actuator?
- What are the most frequently asked Spring Boot interview questions?

Unlike Module 1 (Spring Core), this module focuses on **how a Spring Boot application starts, configures itself, and becomes production-ready**.

---

# Table of Contents

## Chapter 1 — Introduction to Spring Boot

1. Problems Before Spring Boot
2. Why Spring Boot Was Created
3. Spring Framework vs Spring Boot
4. Spring Boot Architecture Overview
5. Complete Startup Lifecycle
6. Real Production Example
7. Best Practices
8. Interview Questions

---

## Chapter 2 — `@SpringBootApplication`

- Internal Composition
- `@Configuration`
- `@EnableAutoConfiguration`
- `@ComponentScan`
- Internal Processing

---

## Chapter 3 — SpringApplication.run()

- Startup Flow
- Environment Preparation
- Context Creation
- Bean Loading
- Embedded Server Startup

---

## Chapter 4 — Starter Dependencies

- Why Starters Exist
- Parent POM
- Dependency Management (BOM)
- Common Starters
- Maven vs Gradle

---

## Chapter 5 — Auto Configuration

- What is Auto Configuration?
- `AutoConfiguration.imports`
- Conditional Annotations
- Bean Registration
- Custom Auto Configuration

---

## Chapter 6 — Configuration Files

- application.properties
- application.yml
- Property Resolution
- Property Precedence
- Environment Variables

---

## Chapter 7 — Profiles

- Dev
- QA
- Staging
- Production
- `@Profile`

---

## Chapter 8 — Configuration Properties

- `@ConfigurationProperties`
- `@Value`
- Relaxed Binding
- Validation

---

## Chapter 9 — Embedded Servers

- Embedded Tomcat
- Jetty
- Undertow
- Startup Flow

---

## Chapter 10 — Spring Boot Actuator

- Health
- Metrics
- Info
- Monitoring
- Security

---

## Chapter 11 — Production Best Practices

---

## Chapter 12 — Top Interview Questions

---

## Chapter 13 — One Page Revision

---

# Chapter 1 — Introduction to Spring Boot

---

# Interview Frequency

⭐⭐⭐⭐⭐ (Extremely High)

Every Spring Boot interview starts with one of these questions.

Typical Questions:

- What is Spring Boot?
- Why was Spring Boot introduced?
- Spring vs Spring Boot?
- Why not use Spring Framework directly?
- What are the advantages of Spring Boot?

---

# 1. The Problem Before Spring Boot

To understand Spring Boot, you first need to understand what development looked like before it existed.

Imagine you're asked to build a simple REST API for a crypto staking platform.

Today's expectation is straightforward:

```
GET /chains
POST /stakes
GET /wallets
```

Now imagine doing this in **traditional Spring Framework (before Spring Boot)**.

You had to configure almost everything manually.

A typical project required:

- Creating a Maven project from scratch.
- Finding compatible Spring versions.
- Adding each dependency manually.
- Configuring `DispatcherServlet`.
- Configuring component scanning.
- Configuring transaction management.
- Configuring Hibernate.
- Configuring the DataSource.
- Configuring the embedded or external web server.
- Writing XML configuration (in older projects) or extensive Java configuration.

Even a simple REST API involved a significant amount of setup before writing business logic.

---

## Example: Creating a REST API Before Spring Boot

Developers often had to manage dependencies like:

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
</dependency>

<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-core</artifactId>
</dependency>

<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
</dependency>
```

Then configure each framework separately.

---

## Manual Servlet Configuration

A developer also needed to register the main servlet.

Conceptually:

```
Web Server

↓

DispatcherServlet

↓

Spring MVC

↓

Controllers
```

Earlier, this often meant editing deployment descriptors (`web.xml`) or writing explicit configuration classes.

---

## Database Configuration

A DataSource also had to be configured manually.

Example (simplified):

```java
@Bean
public DataSource dataSource() {

    DriverManagerDataSource ds = new DriverManagerDataSource();

    ds.setDriverClassName("com.mysql.cj.jdbc.Driver");
    ds.setUrl("jdbc:mysql://localhost:3306/staking");
    ds.setUsername("root");
    ds.setPassword("password");

    return ds;
}
```

Every infrastructure component required explicit configuration.

---

## Dependency Compatibility

Another major issue was version compatibility.

Imagine using:

```
Spring Core 5.x

↓

Spring MVC 4.x

↓

Hibernate 6.x

↓

Tomcat 8.x
```

Some combinations simply didn't work together.

Developers had to determine which versions were compatible, often by reading documentation or community forums.

---

# Real Problems Before Spring Boot

Let's summarize the major pain points.

### 1. Too Much Boilerplate

Projects contained many configuration files before any business feature was implemented.

---

### 2. Version Conflicts

Incorrect dependency versions could prevent the application from starting.

---

### 3. Manual Configuration

Developers repeatedly configured:

- DataSource
- DispatcherServlet
- Jackson
- Hibernate
- Transaction Manager
- View Resolver
- Component Scan

for almost every project.

---

### 4. Slow Project Setup

Creating a new backend service could take hours—or even days—before the first endpoint was written.

---

### 5. Difficult for Beginners

Developers needed to understand many Spring internals just to create a simple "Hello World" application.

---

# Real-World Analogy

Imagine buying a new laptop.

### Traditional Spring

The laptop arrives as individual parts:

- Motherboard
- CPU
- RAM
- SSD
- Cooling System
- Operating System

You assemble everything yourself.

It gives you maximum control—but requires expertise.

---

### Spring Boot

The laptop arrives fully assembled.

You simply:

- Turn it on.
- Install your applications.
- Start working.

You still have full control if you need it, but sensible defaults are already provided.

This is exactly Spring Boot's philosophy:

> **Convention over configuration.**

Instead of asking the developer to configure everything manually, Spring Boot provides intelligent defaults that work for the majority of applications.

---

# Interview Definition

> **Spring Boot is an opinionated extension of the Spring Framework that simplifies application development by providing auto-configuration, starter dependencies, embedded servers, and production-ready features, allowing developers to focus on business logic instead of infrastructure configuration.**

This is a strong interview-ready definition because it explains both **what Spring Boot is** and **why it exists**.

---

# Core Philosophy

Spring Boot is built around three main ideas:

1. **Reduce Configuration**
2. **Provide Sensible Defaults**
3. **Enable Rapid Application Development**

Everything you'll learn in this module supports one or more of these goals.

---

# Spring Framework vs Spring Boot (Overview)

| Spring Framework | Spring Boot |
|------------------|-------------|
| Core framework | Built on top of Spring Framework |
| Manual configuration | Auto Configuration |
| Manual dependency selection | Starter Dependencies |
| External server often required | Embedded server by default |
| More boilerplate | Minimal boilerplate |
| Greater setup effort | Faster development |

> **Important Interview Note:** Spring Boot is **not a replacement** for the Spring Framework. It is built on top of it. Every Spring Boot application is fundamentally a Spring application with additional conveniences.

---

# What's Next?

In the next section, we'll answer the most common follow-up interview question:

> **"How does Spring Boot actually reduce configuration?"**

We'll introduce the four pillars of Spring Boot:

- Starter Dependencies
- Auto Configuration
- Embedded Servers
- Production Features

These concepts form the foundation for everything else in this module.

---

**(Continue in Module 2 – Part 2)**

---

# 2. Why Spring Boot Was Created

Now that we understand the pain points of traditional Spring Framework, let's answer the obvious question:

> **Why did Pivotal (now VMware/Broadcom) create Spring Boot?**

Spring Boot was **not** created because Spring Framework was bad.

Spring Framework was already one of the best enterprise Java frameworks.

The problem was that developers spent too much time configuring infrastructure instead of writing business logic.

The goal of Spring Boot was simple:

> **"Let developers write business logic, not configuration."**

---

# Evolution of Spring

```
Java EE

↓

Complex XML Configuration

↓

Spring Framework (IoC + DI)

↓

Less Boilerplate

↓

Still Too Much Configuration

↓

Spring Boot

↓

Convention over Configuration

↓

Rapid Development
```

Spring Boot is therefore the **next evolutionary step** of the Spring ecosystem.

---

# The Four Pillars of Spring Boot

Everything Spring Boot offers revolves around four major features.

```
                Spring Boot
                     │
      ┌──────────────┼──────────────┐
      │              │              │
      ▼              ▼              ▼
Starter        Auto          Embedded
Dependencies Configuration    Servers
                     │
                     ▼
           Production Features
```

Let's understand each one.

---

# Pillar 1 — Starter Dependencies

Before Spring Boot:

Imagine you wanted to build a REST API.

You had to manually search for:

- spring-core
- spring-context
- spring-web
- spring-webmvc
- jackson
- validation-api
- servlet-api
- logging libraries
- compatible versions

Your `pom.xml` became very large.

With Spring Boot:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

That's it.

Spring Boot automatically brings in:

- Spring MVC
- Jackson
- Validation
- Embedded Tomcat
- Logging
- Spring Core
- Required transitive dependencies

without you listing each dependency individually.

---

## Why Are They Called "Starters"?

Think of a starter as a **pre-packaged toolkit**.

Instead of buying:

- Hammer
- Screwdriver
- Wrench
- Drill

separately,

you buy one toolbox.

Similarly,

```
spring-boot-starter-web

↓

Contains

↓

Spring MVC

↓

Jackson

↓

Tomcat

↓

Validation

↓

Logging

↓

Spring Core
```

One dependency gives you everything required for a web application.

---

# Pillar 2 — Auto Configuration

Suppose your project contains:

```xml
spring-boot-starter-data-jpa
```

and

```properties
spring.datasource.url=...
```

You never create:

```java
DataSource
```

or

```java
EntityManagerFactory
```

manually.

Why?

Because Spring Boot notices:

```
Hibernate Present

↓

Datasource Properties Present

↓

Automatically Configure JPA
```

This is called **Auto Configuration**.

We'll study this in great detail later because it's one of the most important Spring Boot interview topics.

---

# Pillar 3 — Embedded Server

Traditional Spring applications usually required deploying the application to an external server such as:

- Tomcat
- JBoss
- WebLogic
- WebSphere

Deployment looked like this:

```
Build WAR

↓

Install Tomcat

↓

Deploy WAR

↓

Restart Server
```

Spring Boot changed everything.

Now the web server is packaged inside your application.

```
main()

↓

SpringApplication.run()

↓

Embedded Tomcat Starts

↓

Application Ready
```

You simply run:

```bash
java -jar application.jar
```

and your application starts.

---

# Pillar 4 — Production-Ready Features

Modern backend services require much more than REST APIs.

Examples:

- Health monitoring
- Metrics
- Logging
- Configuration management
- Externalized properties

Spring Boot includes these capabilities through features such as:

- Actuator
- Profiles
- External configuration
- Built-in logging support

These make applications easier to operate in production.

---

# What Does "Opinionated" Mean?

One of the most frequently misunderstood interview terms.

Interviewers often ask:

> **What does it mean that Spring Boot is opinionated?**

It does **not** mean that Spring Boot forces developers to do things a certain way.

Instead, it means:

> Spring Boot provides **sensible default configurations** that work for most applications.

You are always free to override them.

---

## Example

Without Spring Boot:

You configure:

- Jackson
- DispatcherServlet
- Tomcat
- View Resolver
- Message Converters

manually.

With Spring Boot:

```
Defaults Applied

↓

Application Starts

↓

Override Only If Needed
```

Spring Boot says:

> "I'll make the common case easy."

---

# Convention Over Configuration

This phrase appears in many interviews.

## Definition

Convention over Configuration means:

> If developers follow common conventions, the framework automatically configures the application with minimal explicit configuration.

---

## Example

Create a controller:

```java
@RestController
public class ChainController {

}
```

Spring Boot automatically discovers it because:

- Component scanning is enabled.
- Default package conventions are followed.

You didn't write:

```xml
<bean .../>
```

or

```java
@Bean
```

for the controller.

---

# Spring Framework vs Spring Boot

This comparison is almost guaranteed to be asked.

| Feature | Spring Framework | Spring Boot |
|----------|------------------|-------------|
| Type | Framework | Extension built on Spring |
| Configuration | Mostly manual | Mostly automatic |
| Dependency Management | Manual | Starter Dependencies |
| Server | External | Embedded by default |
| Project Setup | Slower | Very Fast |
| Production Features | Additional setup | Built-in support |
| Auto Configuration | No | Yes |

---

# Is Spring Boot a Replacement for Spring?

**No.**

This is one of the biggest misconceptions.

Relationship:

```
Spring Boot

↓

Uses

↓

Spring Framework
```

Every Spring Boot application still uses:

- IoC Container
- Dependency Injection
- Beans
- ApplicationContext
- AOP
- Transactions

All of those concepts come from the Spring Framework.

Spring Boot simply makes them easier to use.

---

# Real Production Example

Let's use our running example—a crypto staking platform.

Without Spring Boot, creating a new microservice for **Chain Onboarding** might involve:

- Configuring Tomcat
- Configuring Spring MVC
- Configuring Hibernate
- Configuring Jackson
- Configuring Logging
- Configuring Validation
- Configuring Transactions
- Configuring Component Scan

Only after completing all of that could the developer write:

```java
@PostMapping("/chains")
```

With Spring Boot:

```
spring-boot-starter-web

+

spring-boot-starter-data-jpa

+

application.yml

↓

Business Logic
```

The infrastructure is largely ready from the beginning.

---

# Advantages of Spring Boot

1. Rapid development.
2. Reduced boilerplate.
3. Simplified dependency management.
4. Embedded web servers.
5. Auto configuration.
6. Easy microservice development.
7. Production-ready tooling.
8. Excellent testing support.
9. Strong community and ecosystem.

---

# Best Practices

✅ Accept Spring Boot defaults unless you have a good reason to customize them.

✅ Avoid unnecessary configuration classes.

✅ Prefer Starter Dependencies over manually managing library versions.

✅ Understand what Spring Boot configures automatically before overriding it.

---

# Interview Perspective

## Question

Why was Spring Boot introduced?

**Excellent Answer:**

> Spring Boot was introduced to simplify Spring application development by reducing boilerplate configuration. It provides auto-configuration, starter dependencies, embedded web servers, and production-ready features so developers can focus on business logic instead of infrastructure setup.

---

## Question

What are the four pillars of Spring Boot?

**Excellent Answer:**

1. Starter Dependencies
2. Auto Configuration
3. Embedded Servers
4. Production-Ready Features

---

## Question

Is Spring Boot a replacement for Spring Framework?

**Excellent Answer:**

> No. Spring Boot is built on top of the Spring Framework. It uses all core Spring concepts such as IoC, Dependency Injection, ApplicationContext, AOP, and Transactions while providing sensible defaults and reducing configuration effort.

---

# Common Interview Traps

### Trap 1

❌ Spring Boot and Spring Framework are different technologies.

✔ Spring Boot is built on top of Spring Framework.

---

### Trap 2

❌ Spring Boot removes the need to understand Spring.

✔ Spring Boot simplifies configuration, but understanding Spring Core is essential for debugging, performance tuning, and interviews.

---

### Trap 3

❌ Spring Boot forces developers to use its defaults.

✔ Spring Boot provides defaults, but almost every aspect can be customized or overridden.

---

# Summary

Remember these key points:

- Spring Boot simplifies Spring, it does not replace it.
- It is built around four pillars:
  - Starter Dependencies
  - Auto Configuration
  - Embedded Servers
  - Production Features
- The philosophy is **Convention over Configuration**.
- Spring Boot reduces setup time while preserving the flexibility of the Spring Framework.

---

# Coming Next

In the next section, we'll study:

# Chapter 1 (continued)

## Spring Boot Architecture Overview

We'll answer:

- What are the internal layers of Spring Boot?
- Where does `SpringApplication.run()` fit?
- How does the application bootstrap itself?
- How do Starter Dependencies, Auto Configuration, and the Spring Container work together?
- What is the complete startup lifecycle before a single HTTP request reaches your controller?

This is where we'll begin looking at Spring Boot from the inside rather than just using it.


---

# Chapter 1 (Continued)

# 3. Spring Boot Architecture Overview

---

# Interview Frequency

⭐⭐⭐⭐⭐ (Extremely High)

Most developers know how to **use** Spring Boot.

Good interviewers want to know if you understand **how it works internally**.

Typical Questions:

- Explain Spring Boot Architecture.
- What happens internally when a Spring Boot application starts?
- Which components are involved in startup?
- Where does Auto Configuration fit?
- Where is Embedded Tomcat started?

---

# Goal

By the end of this section you should understand:

- Complete Spring Boot architecture
- Major internal components
- Startup pipeline
- Responsibilities of each component
- How everything connects together

---

# High-Level Architecture

A Spring Boot application is **not a monolithic framework**.

Instead, it is built by combining multiple layers.

```
                    Spring Boot Application
                             │
                             ▼
                   SpringApplication.run()
                             │
                             ▼
                  SpringApplication Class
                             │
                             ▼
                  ApplicationContext Created
                             │
        ┌────────────────────┼────────────────────┐
        ▼                    ▼                    ▼
 Component Scan      Auto Configuration      Environment
        │                    │                    │
        ▼                    ▼                    ▼
 Bean Definitions      Infrastructure      Properties
        │                    │
        └──────────────┬─────┘
                       ▼
               Bean Creation
                       │
                       ▼
            Embedded Web Server
                       │
                       ▼
              DispatcherServlet
                       │
                       ▼
               Application Ready
```

This diagram represents the entire lifecycle from executing `main()` until the application is ready to accept HTTP requests.

---

# Understanding the Layers

Let's walk through each component.

---

# Layer 1 — Your Application

Everything starts here.

Example:

```java
@SpringBootApplication
public class StakingApplication {

    public static void main(String[] args) {

        SpringApplication.run(StakingApplication.class, args);

    }

}
```

This is the only code most developers write to start a Spring Boot application.

Yet internally, thousands of lines of Spring Boot code execute.

---

# Layer 2 — SpringApplication

`SpringApplication` is the **bootstrap class** of Spring Boot.

Its responsibilities include:

- Creating the appropriate `ApplicationContext`
- Preparing the environment
- Loading configuration files
- Starting logging
- Performing component scanning
- Running auto-configuration
- Starting the embedded server
- Publishing startup events

Think of it as the **conductor of an orchestra**.

It doesn't perform every task itself.

Instead, it coordinates many Spring components.

---

# Layer 3 — Environment

Before Spring creates beans, it first prepares the application's environment.

The Environment contains configuration from many sources.

Examples:

```
application.properties

application.yml

↓

Environment Variables

↓

Command-Line Arguments

↓

System Properties

↓

Default Values
```

Spring combines these sources into a unified configuration model.

Later, when you write:

```java
@Value("${server.port}")
```

or

```java
@ConfigurationProperties
```

the values come from the Environment.

We'll study this in detail later.

---

# Layer 4 — ApplicationContext

After preparing the Environment,

Spring creates the IoC container.

Usually:

```
ApplicationContext
```

Responsibilities:

- Bean management
- Dependency Injection
- Lifecycle callbacks
- Component scanning
- Bean scopes
- Event publishing

Notice that Spring Boot **does not replace** the ApplicationContext.

It still uses the same Spring container you studied in Module 1.

---

# Layer 5 — Component Scanning

Now Spring begins discovering your application classes.

Suppose your project contains:

```
controller/

service/

repository/

config/

security/

exception/
```

Spring scans packages and finds:

```
@RestController

↓

@Service

↓

@Repository

↓

@Component

↓

@Configuration
```

Each discovered class becomes a **BeanDefinition**.

Remember from Module 1:

```
Component Scan

↓

BeanDefinition

↓

Bean Creation
```

Spring Boot builds on this exact mechanism.

---

# Layer 6 — Auto Configuration

This is where Spring Boot becomes different from plain Spring Framework.

Suppose your dependencies include:

```
spring-boot-starter-web

↓

spring-boot-starter-data-jpa

↓

spring-boot-starter-security
```

Spring Boot examines the classpath.

Conceptually:

```
Spring MVC Present?

↓

Configure MVC

-------------------

Hibernate Present?

↓

Configure JPA

-------------------

Tomcat Present?

↓

Configure Embedded Server

-------------------

Jackson Present?

↓

Configure JSON Support
```

You don't write these configurations manually.

Spring Boot decides what infrastructure beans should be created.

This is called **Auto Configuration**.

---

# Layer 7 — Bean Creation

After:

- Component Scanning
- Auto Configuration

Spring creates beans.

Example:

```
ChainController

↓

ChainService

↓

ChainRepository

↓

ObjectMapper

↓

DataSource

↓

EntityManager

↓

DispatcherServlet
```

Some beans come from your code.

Others are created automatically by Spring Boot.

---

# Layer 8 — Embedded Web Server

If your application is a web application,

Spring Boot starts an embedded server.

Usually:

```
Embedded Tomcat
```

Startup flow:

```
Create Tomcat

↓

Register DispatcherServlet

↓

Open Port

↓

Listen For Requests
```

No separate Tomcat installation is required.

---

# Layer 9 — DispatcherServlet

Once Tomcat starts,

Spring MVC registers:

```
DispatcherServlet
```

This becomes the front controller.

Every HTTP request follows this flow:

```
Browser

↓

Tomcat

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
```

We'll study the request lifecycle in Module 3.

---

# Complete Startup Lifecycle

Let's combine everything.

```
main()

↓

SpringApplication.run()

↓

Create Environment

↓

Create ApplicationContext

↓

Component Scan

↓

Auto Configuration

↓

Register BeanDefinitions

↓

Instantiate Beans

↓

Dependency Injection

↓

@PostConstruct

↓

Embedded Tomcat Starts

↓

DispatcherServlet Registered

↓

Application Ready
```

If an interviewer asks:

> **"What happens when a Spring Boot application starts?"**

This is the diagram they expect you to explain.

---

# Architecture Applied to Our Staking Platform

Suppose we're building a staking backend.

Project:

```
com.staking.platform

├── controller
├── service
├── repository
├── config
├── security
├── entity
└── dto
```

Startup:

```
SpringApplication.run()

↓

ApplicationContext

↓

Find ChainController

↓

Find StakeService

↓

Find StakeRepository

↓

Configure DataSource

↓

Configure Hibernate

↓

Configure ObjectMapper

↓

Configure Embedded Tomcat

↓

Application Ready
```

Notice how Spring Boot configures both:

- Your application beans
- Framework infrastructure beans

---

# Internal Responsibility Diagram

```
Developer Writes

↓

Business Classes

↓

Spring Boot

↓

Infrastructure

↓

Spring Framework

↓

IoC Container

↓

JVM
```

This layered separation is one reason Spring Boot applications are clean and maintainable.

---

# Best Practices

✅ Keep your main application class in the root package.

This allows component scanning to discover subpackages automatically.

Example:

```
com.company.app

↓

controller

service

repository
```

Avoid placing the main class deep inside a subpackage.

---

✅ Let Spring Boot perform auto-configuration unless customization is necessary.

Avoid creating configuration classes that duplicate Spring Boot's defaults.

---

✅ Understand which beans are created by Spring Boot versus your own code.

This knowledge is invaluable when debugging startup issues.

---

# Interview Perspective

## Question

Explain Spring Boot Architecture.

**Excellent Answer:**

> A Spring Boot application starts with `SpringApplication.run()`, which prepares the environment, creates the ApplicationContext, performs component scanning, executes auto-configuration, registers bean definitions, creates beans, starts the embedded web server, registers the DispatcherServlet, and finally makes the application ready to serve requests.

---

## Question

What are the major components of Spring Boot Architecture?

**Expected Answer:**

- SpringApplication
- Environment
- ApplicationContext
- Component Scanning
- Auto Configuration
- Bean Factory
- Embedded Server
- DispatcherServlet

---

## Question

Where does Auto Configuration fit into startup?

**Expected Answer:**

> After the ApplicationContext is created, Spring Boot evaluates the application's dependencies and environment to determine which infrastructure beans should be configured automatically before the application starts.

---

# Common Interview Traps

### Trap 1

❌ Spring Boot creates beans before component scanning.

✔ Component scanning discovers candidate classes and creates BeanDefinitions first. Bean instantiation happens afterward.

---

### Trap 2

❌ Embedded Tomcat starts before the ApplicationContext.

✔ The ApplicationContext is created and initialized before the embedded server is fully started.

---

### Trap 3

❌ Spring Boot replaces the Spring Container.

✔ Spring Boot still uses the same ApplicationContext and IoC container from the Spring Framework.

---

# Summary

Remember this startup sequence:

```
main()

↓

SpringApplication.run()

↓

Environment

↓

ApplicationContext

↓

Component Scan

↓

Auto Configuration

↓

Bean Creation

↓

Embedded Tomcat

↓

DispatcherServlet

↓

Application Ready
```

If you understand this flow, the rest of Spring Boot becomes much easier because every feature fits somewhere in this startup pipeline.

---

# Coming Next

We're now ready for one of the **most important chapters in the entire handbook**:

# Chapter 2 — `@SpringBootApplication`

We'll answer:

- What exactly is `@SpringBootApplication`?
- Why is it considered a **meta-annotation**?
- How does it combine three annotations into one?
- What are `@Configuration`, `@EnableAutoConfiguration`, and `@ComponentScan` doing internally?
- What happens if you remove one of them?
- Why is the location of the main class so important?

This chapter is asked in almost every Spring Boot interview and forms the foundation for understanding Auto Configuration.

---

# Chapter 2 — `@SpringBootApplication`

> **Interview Importance:** ⭐⭐⭐⭐⭐ (One of the Most Asked Spring Boot Topics)

This is probably the annotation every Spring Boot developer has used thousands of times.

Ironically, very few developers know what it actually does.

A very common interview progression is:

> **Interviewer:** What is `@SpringBootApplication`?

Candidate:

> "It starts the application."

Interviewer:

> "Okay... how?"

This chapter answers that "how."

---

# Goal

By the end of this chapter you should understand:

- What `@SpringBootApplication` is
- Why it is called a meta-annotation
- Internal composition
- Startup sequence
- Component Scanning
- Auto Configuration
- Configuration Classes
- Common interview traps

---

# Table of Contents

1. What is `@SpringBootApplication`?
2. Why Was It Introduced?
3. Internal Composition
4. `@Configuration`
5. `@EnableAutoConfiguration`
6. `@ComponentScan`
7. Startup Flow
8. Internal Processing
9. Real Production Example
10. Best Practices
11. Interview Questions

---

# 1. What is `@SpringBootApplication`?

Most Spring Boot applications begin like this:

```java
@SpringBootApplication
public class StakingApplication {

    public static void main(String[] args) {

        SpringApplication.run(StakingApplication.class, args);

    }

}
```

Looks simple.

But internally,

this single annotation activates a large portion of the Spring Boot framework.

---

# Interview Definition

> `@SpringBootApplication` is a **meta-annotation** that combines multiple Spring annotations to enable Java configuration, component scanning, and Spring Boot auto-configuration.

This definition is far stronger than saying:

> "It starts Spring Boot."

Because technically,

`SpringApplication.run()` starts the application.

`@SpringBootApplication` **configures** it.

---

# What Does "Meta-Annotation" Mean?

A meta-annotation is simply:

> **An annotation built using other annotations.**

Instead of writing:

```java
@Configuration
@ComponentScan
@EnableAutoConfiguration
```

Spring Boot allows:

```java
@SpringBootApplication
```

It is a convenience annotation.

---

# Internal Composition

Internally,

`@SpringBootApplication` looks conceptually like this:

```java
@Configuration

@EnableAutoConfiguration

@ComponentScan
```

These three annotations work together.

```
@SpringBootApplication

│

├── @Configuration

├── @EnableAutoConfiguration

└── @ComponentScan
```

This diagram alone answers many interview questions.

---

# Why Was It Introduced?

Imagine writing this in every project:

```java
@Configuration

@ComponentScan

@EnableAutoConfiguration

public class App {

}
```

Every project would repeat the same boilerplate.

Spring Boot combines them into one annotation.

Cleaner.

Less error-prone.

Easier for beginners.

---

# Responsibility of Each Annotation

```
@SpringBootApplication

↓

@Configuration

↓

Bean Definitions

----------------------

@ComponentScan

↓

Discover Components

----------------------

@EnableAutoConfiguration

↓

Configure Infrastructure
```

We'll now study each one individually.

---

# 2. `@Configuration`

You already studied this in Module 1.

Quick revision:

```
@Configuration

↓

Java Configuration

↓

@Bean Methods

↓

Spring Beans
```

Example:

```java
@Configuration
public class AppConfig {

    @Bean
    public ObjectMapper objectMapper() {

        return new ObjectMapper();

    }

}
```

During startup,

Spring processes these configuration classes.

---

# Why Is It Included?

Because almost every Boot application contains configuration.

Examples:

- ObjectMapper
- Redis
- Kafka
- WebClient
- Cache Managers
- Executors

Instead of requiring developers to explicitly enable configuration,

Boot enables it automatically.

---

# 3. `@ComponentScan`

One of Spring's most important features.

Suppose your project looks like:

```
com.staking.platform

│

├── controller

├── service

├── repository

├── config

├── security

└── dto
```

When Boot starts,

it scans packages looking for:

```
@Component

@Service

@Repository

@Controller

@RestController

@Configuration
```

Each discovered class becomes a candidate bean.

---

# Component Scan Flow

```
@ComponentScan

↓

Scan Packages

↓

Find Classes

↓

Create BeanDefinitions

↓

Register Beans
```

Exactly the same Spring Core concept you learned in Module 1.

Spring Boot simply enables it automatically.

---

# Base Package Rule

This is one of the most commonly asked interview questions.

Suppose:

```
com.company

↓

Application.java
```

Everything below:

```
controller

service

repository

config
```

is scanned automatically.

---

Now suppose:

```
com.company.controller

↓

Application.java
```

Will Spring find:

```
repository

service
```

No.

Because they are outside the scanning hierarchy.

---

# Visual Example

Correct:

```
com.company

│

├── Application

├── controller

├── service

├── repository
```

Incorrect:

```
com.company.controller

│

├── Application

│

repository

service
```

The application class should generally be placed in the **root package**.

---

# Why?

Because `@ComponentScan`

by default scans:

```
Current Package

+

All Subpackages
```

It does **not** scan sibling or parent packages automatically.

---

# Production Example

```
com.bg.staking

│

├── StakingApplication

├── controller

├── service

├── repository

├── security

├── config
```

Everything is discovered.

No manual scanning configuration required.

---

# 4. `@EnableAutoConfiguration`

This is the most powerful part of Spring Boot.

Without it,

Spring Boot becomes almost ordinary Spring.

---

# Responsibility

```
@EnableAutoConfiguration

↓

Inspect Classpath

↓

Inspect Properties

↓

Choose Configurations

↓

Create Infrastructure Beans
```

Notice:

It creates **framework beans**,

not your business classes.

Examples:

```
DispatcherServlet

↓

DataSource

↓

Jackson

↓

Hibernate

↓

Validator

↓

TransactionManager
```

You didn't write these.

Spring Boot did.

---

# Example

Suppose your project contains:

```
spring-boot-starter-web
```

Boot says:

```
Spring MVC Present

↓

Configure DispatcherServlet

↓

Configure Jackson

↓

Configure MessageConverters

↓

Configure Embedded Tomcat
```

Now suppose:

```
spring-boot-starter-data-jpa
```

Boot detects:

```
Hibernate Found

↓

Datasource Present

↓

Configure EntityManager

↓

Configure TransactionManager
```

This is Auto Configuration.

---

# Important Observation

Component Scanning creates:

```
Your Beans
```

Auto Configuration creates:

```
Framework Beans
```

Interviewers love this distinction.

---

# Combined Startup Picture

```
@SpringBootApplication

│

├── Component Scan

│       │

│       ▼

│   User Beans

│

└── Auto Configuration

        │

        ▼

 Infrastructure Beans

        │

        ▼

Application Ready
```

---

# Real Startup Example

Crypto Staking Service

Your code:

```
StakeController

StakeService

StakeRepository
```

Spring Boot creates:

```
DispatcherServlet

ObjectMapper

Embedded Tomcat

DataSource

EntityManager

Validator

TransactionManager
```

Together,

they become one running application.

---

# Best Practices

✅ Place the application class in the root package.

✅ Avoid manually configuring beans that Boot already provides unless customization is required.

✅ Understand the difference between your beans and Boot's infrastructure beans.

✅ Do not disable auto-configuration unless you have a clear reason.

---

# Interview Perspective

## Question

What is `@SpringBootApplication`?

**Excellent Answer:**

> `@SpringBootApplication` is a meta-annotation that combines `@Configuration`, `@ComponentScan`, and `@EnableAutoConfiguration`. It enables Java configuration, automatically discovers application components, and configures infrastructure beans based on the application's dependencies and environment.

---

## Question

Why is it called a meta-annotation?

**Answer:**

Because it is composed of multiple annotations that together provide the complete Spring Boot configuration.

---

## Question

What happens if I remove `@ComponentScan`?

**Expected Answer:**

Your controllers, services, repositories, and other application beans will no longer be discovered automatically, leading to missing bean errors such as `NoSuchBeanDefinitionException`.

---

## Question

What happens if I remove `@EnableAutoConfiguration`?

**Expected Answer:**

Spring Boot will stop automatically configuring infrastructure components such as the embedded server, `DispatcherServlet`, `DataSource`, and many other framework beans. You would need to configure them manually.

---

# Common Interview Traps

### Trap 1

❌ `@SpringBootApplication` starts the application.

✔ `SpringApplication.run()` starts the application.

`@SpringBootApplication` provides the configuration required for startup.

---

### Trap 2

❌ Component Scanning creates every bean.

✔ Component Scanning discovers your application beans.

Auto Configuration creates many framework infrastructure beans.

---

### Trap 3

❌ The application class can be placed anywhere.

✔ By convention, place it in the root package so that component scanning can discover all subpackages.

---

# Summary

Remember:

```
@SpringBootApplication

↓

@Configuration

↓

Java Configuration

-------------------

@ComponentScan

↓

Your Beans

-------------------

@EnableAutoConfiguration

↓

Framework Beans

-------------------

Application Ready
```

This mental model will help you answer most interview questions about Spring Boot startup.

---

# Coming Next

The next section is one of the **deepest topics in the Spring Boot module**:

# Chapter 3 — `SpringApplication.run()`

We'll trace the complete execution flow:

```
main()

↓

SpringApplication.run()

↓

Prepare Environment

↓

Create ApplicationContext

↓

Load Configuration

↓

Run Auto Configuration

↓

Create Beans

↓

Start Embedded Tomcat

↓

Register DispatcherServlet

↓

Application Ready
```

This is the chapter where we'll uncover what actually happens inside the thousands of lines of Spring Boot startup code.

---

# Chapter 3 — `SpringApplication.run()`

> **Interview Importance:** ⭐⭐⭐⭐⭐ (Top 5 Most Asked Spring Boot Topics)

This is one of the deepest Spring Boot interview topics.

Most developers know this line:

```java
public static void main(String[] args) {

    SpringApplication.run(StakingApplication.class, args);

}
```

But very few know what happens internally.

A good interviewer will eventually ask:

> **"Walk me through what happens after `SpringApplication.run()` is called."**

If you can answer this confidently, you immediately stand out because you're demonstrating an understanding of Spring Boot internals rather than just knowing annotations.

---

# Goal

By the end of this chapter, you should understand:

- What `SpringApplication` is
- What `run()` does internally
- The complete startup lifecycle
- Environment preparation
- ApplicationContext creation
- Bean registration
- Event publishing
- Embedded Tomcat startup
- Common interview questions

---

# Table of Contents

1. What is `SpringApplication`?
2. High-Level Startup Flow
3. Internal Startup Sequence
4. Step-by-Step Deep Dive
5. Events During Startup
6. Production Example
7. Best Practices
8. Interview Questions

---

# 1. What is `SpringApplication`?

`SpringApplication` is the **bootstrap class** provided by Spring Boot.

Think of it as the **orchestrator** of the startup process.

It doesn't create every object itself.

Instead, it coordinates the startup of many Spring Framework components.

Responsibilities include:

- Preparing the Environment
- Creating the ApplicationContext
- Loading configuration
- Running Auto Configuration
- Performing Component Scanning
- Creating beans
- Starting the embedded server
- Publishing startup events

---

# Real-World Analogy

Imagine opening a new shopping mall.

One manager doesn't:

- Build the shops
- Hire security
- Install electricity
- Clean the floors

Instead, the manager coordinates all teams.

`SpringApplication` plays the same role.

It coordinates the startup process but delegates the actual work to specialized Spring components.

---

# High-Level Startup Flow

Let's first understand the complete picture before diving into each step.

```text
main()

↓

SpringApplication.run()

↓

Create SpringApplication Object

↓

Prepare Environment

↓

Create ApplicationContext

↓

Load Initializers

↓

Load Listeners

↓

Component Scan

↓

Auto Configuration

↓

Create Bean Definitions

↓

Instantiate Beans

↓

Dependency Injection

↓

@PostConstruct

↓

Start Embedded Tomcat

↓

Register DispatcherServlet

↓

Application Ready
```

This is the lifecycle you should memorize.

---

# Who Performs What?

Many developers think `SpringApplication.run()` directly creates beans.

Not true.

Let's assign responsibilities.

| Component | Responsibility |
|------------|----------------|
| SpringApplication | Coordinates startup |
| Environment | Loads configuration |
| ApplicationContext | IoC Container |
| Component Scanner | Finds user beans |
| Auto Configuration | Creates infrastructure beans |
| BeanFactory | Creates bean instances |
| Embedded Tomcat | Starts HTTP server |
| DispatcherServlet | Handles HTTP requests |

Understanding this separation is crucial.

---

# Internal Startup Pipeline

Internally, the flow looks conceptually like this:

```text
SpringApplication.run()

        │

        ▼

Prepare Environment

        │

        ▼

Create ApplicationContext

        │

        ▼

Apply Initializers

        │

        ▼

Load Bean Definitions

        │

        ▼

Refresh Context

        │

        ▼

Create Beans

        │

        ▼

Start Web Server

        │

        ▼

Publish Ready Event

        │

        ▼

Application Running
```

Notice the **ApplicationContext refresh**.

This is one of the most important methods in Spring.

We'll revisit it in Module 7 because many Spring internals happen during the refresh phase.

---

# Step 1 — `main()` Method

Everything starts with Java.

```java
public static void main(String[] args) {

    SpringApplication.run(
            StakingApplication.class,
            args);

}
```

At this point:

- JVM starts
- Main thread begins execution
- Spring Boot hasn't created any beans yet

---

# Step 2 — Create `SpringApplication`

Internally, Spring Boot first creates a `SpringApplication` object.

Conceptually:

```java
SpringApplication app =
        new SpringApplication(StakingApplication.class);
```

This object stores information such as:

- Primary source class
- Application type
- Banner mode
- Initializers
- Listeners

The startup process hasn't begun yet.

It is only preparing.

---

# Step 3 — Determine Application Type

Spring Boot asks:

> "What kind of application is this?"

Possible answers:

```
Servlet Application

Reactive Application

Non-Web Application
```

If `spring-webmvc` is on the classpath:

```
Application Type

↓

SERVLET
```

If WebFlux is detected:

```
Application Type

↓

REACTIVE
```

Otherwise:

```
Application Type

↓

NONE
```

This decision influences the type of `ApplicationContext` Spring creates.

---

# Step 4 — Prepare the Environment

This is one of the earliest startup phases.

Spring now collects configuration from multiple sources.

```text
application.properties

↓

application.yml

↓

Environment Variables

↓

System Properties

↓

Command-Line Arguments

↓

Default Values
```

All of these are merged into a single `Environment` object.

Later, whenever you write:

```java
@Value("${server.port}")
```

or

```java
@ConfigurationProperties
```

Spring reads the values from this Environment.

---

# Property Precedence

Suppose the same property exists in multiple places.

Which one wins?

Spring follows an order of precedence.

Simplified:

```text
Command-Line Arguments

↓

Environment Variables

↓

application.yml

↓

application.properties

↓

Default Values
```

Higher-priority sources override lower-priority ones.

We'll study the exact order in the Configuration chapter.

---

# Step 5 — Create the ApplicationContext

Now Spring creates the IoC container.

For a web application, this is usually:

```text
AnnotationConfigServletWebServerApplicationContext
```

Don't worry about memorizing the class name.

For interviews, remember:

```
ApplicationContext

↓

Stores Beans

↓

Performs Dependency Injection

↓

Manages Lifecycle

↓

Publishes Events
```

This is the same Spring container you learned in Module 1.

Spring Boot doesn't invent a new container—it builds upon the existing one.

---

# Step 6 — Apply Initializers

Spring Boot allows developers and libraries to customize the `ApplicationContext` before it is refreshed.

These customizations are performed using **ApplicationContextInitializers**.

Conceptually:

```text
Create Context

↓

Apply Initializers

↓

Continue Startup
```

Most application developers don't write custom initializers.

But many Spring Boot modules use them internally.

---

# Step 7 — Register Listeners

Spring Boot now registers application listeners.

Why?

Because Spring publishes events during startup.

Examples include:

- Starting
- Environment Prepared
- Context Prepared
- Context Loaded
- Started
- Ready
- Failed

Libraries such as logging frameworks and monitoring tools listen to these events.

---

# Startup Timeline So Far

Let's pause and see where we are.

```text
main()

↓

SpringApplication.run()

↓

Create SpringApplication

↓

Determine Application Type

↓

Prepare Environment

↓

Create ApplicationContext

↓

Apply Initializers

↓

Register Listeners

↓

... (Next Steps)
```

No beans have been created yet.

That happens during the **ApplicationContext refresh**, which we'll cover in the next section because it's the heart of the entire startup process.

---

# Best Practices

✅ Keep startup logic minimal inside the `main()` method.

✅ Avoid performing heavy business operations before the application is fully initialized.

✅ Use `ApplicationRunner` or `CommandLineRunner` for startup tasks instead of placing logic directly in `main()`.

---

# Interview Perspective

## Question

What is `SpringApplication`?

**Excellent Answer:**

> `SpringApplication` is the bootstrap class in Spring Boot. It coordinates application startup by preparing the environment, creating the ApplicationContext, performing component scanning, running auto-configuration, creating beans, starting the embedded server, and publishing lifecycle events.

---

## Question

What is the first thing `SpringApplication.run()` does?

**Expected Answer:**

> It creates a `SpringApplication` instance, determines the application type, and begins preparing the startup environment before creating the `ApplicationContext`.

---

# Common Interview Traps

### Trap 1

❌ Beans are created immediately after `SpringApplication.run()` is called.

✔ Bean creation occurs later during the **ApplicationContext refresh** phase.

---

### Trap 2

❌ The Environment is created after the ApplicationContext.

✔ The Environment is prepared **before** the ApplicationContext because the context depends on configuration values.

---

# Summary

So far, the startup sequence is:

```text
main()

↓

SpringApplication.run()

↓

Create SpringApplication

↓

Determine Application Type

↓

Prepare Environment

↓

Create ApplicationContext

↓

Apply Initializers

↓

Register Listeners

↓

(ApplicationContext Refresh - Next Section)
```

This is only the first half of the startup process.

The next section contains the **most important internal method in Spring Boot startup**: **`ApplicationContext.refresh()`**. During this phase, Spring performs component scanning, auto-configuration, bean creation, dependency injection, lifecycle callbacks, and finally starts the embedded web server.

---

# Chapter 3 (Continued)

# 4. ApplicationContext.refresh() — The Heart of Spring Boot Startup

> **Interview Importance:** ⭐⭐⭐⭐⭐

If `SpringApplication.run()` is the **conductor**, then `ApplicationContext.refresh()` is the **engine**.

Almost every important activity during startup happens inside this method.

Many experienced interviewers specifically ask:

> **"What happens during ApplicationContext refresh?"**

If you understand this chapter, you'll understand how Spring Boot really works.

---

# Why is `refresh()` Important?

When Spring creates the `ApplicationContext`, it is initially **empty**.

At that point:

- No beans exist.
- No dependency injection has occurred.
- No embedded server is running.
- No controllers are registered.

`refresh()` transforms an empty container into a fully initialized application.

---

# High-Level Refresh Flow

```text
ApplicationContext.refresh()

↓

Prepare BeanFactory

↓

Load Bean Definitions

↓

Invoke BeanFactoryPostProcessors

↓

Register BeanPostProcessors

↓

Initialize MessageSource

↓

Initialize Event Multicaster

↓

Create Singleton Beans

↓

Dependency Injection

↓

@PostConstruct

↓

Start Embedded Server

↓

Publish ContextRefreshedEvent

↓

Application Ready
```

This sequence is one of the most useful startup diagrams to remember.

---

# Step 1 — Prepare the BeanFactory

The `BeanFactory` is responsible for creating and managing bean instances.

Remember from Module 1:

```text
ApplicationContext

        │

        ▼

DefaultListableBeanFactory
```

The `ApplicationContext` delegates bean creation to the underlying `BeanFactory`.

At this stage, Spring prepares internal infrastructure required for bean management.

---

# Step 2 — Load Bean Definitions

Next, Spring begins loading metadata about beans.

A **BeanDefinition** is not the bean itself.

It is simply a description.

Think of it as a blueprint.

Example:

```text
BeanDefinition

↓

Class Name

↓

Scope

↓

Constructor

↓

Dependencies

↓

Lifecycle Information
```

No object has been created yet.

---

# Example

Suppose we have:

```java
@Service
public class StakeService {

}
```

Spring creates something conceptually like:

```text
BeanDefinition

Bean Name:
stakeService

Class:
StakeService

Scope:
Singleton

Lazy:
False
```

Only metadata is registered.

---

# Step 3 — Component Scanning

Now Spring scans packages.

Suppose your project contains:

```text
controller/

service/

repository/

config/
```

Spring looks for:

```text
@Component

@Service

@Repository

@Controller

@RestController

@Configuration
```

Every matching class becomes a `BeanDefinition`.

Remember:

```text
@ComponentScan

↓

Find Classes

↓

Create BeanDefinitions

↓

Register BeanDefinitions
```

Still no objects have been instantiated.

---

# Step 4 — Invoke BeanFactoryPostProcessors

Now Spring gives itself a chance to modify bean definitions **before** beans are created.

Notice carefully:

These processors work on:

```
BeanDefinition
```

NOT

```
Bean Instance
```

This distinction is frequently asked in interviews.

---

# BeanFactoryPostProcessor

Responsibilities include:

- Modifying bean metadata
- Changing scopes
- Registering additional bean definitions
- Processing configuration classes

Example:

```text
BeanDefinition

↓

Modify Properties

↓

Bean Created Later
```

---

# Interview Question

> **Difference between BeanFactoryPostProcessor and BeanPostProcessor?**

Excellent Answer:

| BeanFactoryPostProcessor | BeanPostProcessor |
|--------------------------|------------------|
| Works on BeanDefinition | Works on Bean Instance |
| Before bean creation | After bean creation |
| Metadata modification | Object customization |

We'll revisit this in Module 7 when discussing Spring internals.

---

# Step 5 — Register BeanPostProcessors

Now Spring registers another important extension point.

These operate **after** bean objects are created.

Flow:

```text
Bean Created

↓

BeanPostProcessor Before

↓

@PostConstruct

↓

Initialization

↓

BeanPostProcessor After

↓

Bean Ready
```

Examples:

- AOP proxies
- Transaction proxies
- Security proxies

Many advanced Spring features rely on `BeanPostProcessor`.

---

# Step 6 — Create Singleton Beans

Now the actual objects are created.

Suppose we have:

```java
@Service
public class StakeService {

}
```

Spring finally executes something conceptually like:

```java
new StakeService();
```

Until this point, the object didn't exist.

Only its metadata existed.

---

# Step 7 — Dependency Injection

Now Spring injects dependencies.

Example:

```java
@Service
public class StakeService {

    private final ChainRepository repository;

    public StakeService(
            ChainRepository repository) {

        this.repository = repository;

    }

}
```

Execution:

```text
Create ChainRepository

↓

Create StakeService

↓

Inject Repository

↓

StakeService Ready
```

Spring follows the dependency graph to ensure required beans are available before injecting them.

---

# Step 8 — Lifecycle Callbacks

After dependencies are injected, lifecycle methods are executed.

Order:

```text
Constructor

↓

Dependency Injection

↓

BeanPostProcessor Before

↓

@PostConstruct

↓

afterPropertiesSet()

↓

Custom init()

↓

BeanPostProcessor After

↓

Bean Ready
```

This sequence builds directly on what you learned in Module 1.

---

# Step 9 — Embedded Tomcat Startup

Once all singleton beans are ready, Spring Boot starts the embedded web server.

```text
Create Tomcat

↓

Configure Connectors

↓

Register DispatcherServlet

↓

Bind Port

↓

Listen for Requests
```

If `server.port=8081`:

Tomcat binds to port **8081**.

If omitted:

Default is **8080**.

---

# Step 10 — Publish Startup Events

Finally, Spring announces:

```
Application Started

↓

Application Ready
```

Frameworks such as:

- Logging
- Monitoring
- Metrics
- Observability

listen for these events.

Your application can also listen to them using event listeners.

---

# Complete Refresh Lifecycle

```text
ApplicationContext.refresh()

↓

Prepare BeanFactory

↓

Load BeanDefinitions

↓

Component Scan

↓

BeanFactoryPostProcessors

↓

Register BeanPostProcessors

↓

Instantiate Singleton Beans

↓

Dependency Injection

↓

@PostConstruct

↓

Start Embedded Tomcat

↓

Publish Ready Event

↓

Application Running
```

This is one of the most important diagrams in the entire Spring Boot module.

---

# Production Example

Our staking platform contains:

```text
ChainController

StakeController

WalletController

↓

StakeService

RewardService

↓

Repositories

↓

Entities
```

During `refresh()`:

- Bean definitions are registered.
- Controllers are instantiated.
- Services are wired.
- Repositories are initialized.
- Hibernate infrastructure is configured.
- Tomcat starts.
- APIs become available.

Only after this sequence can a request like:

```http
GET /chains
```

be handled successfully.

---

# Best Practices

✅ Avoid expensive operations inside constructors.

✅ Use `@PostConstruct` only for lightweight initialization.

✅ Avoid database calls during bean initialization unless absolutely necessary, as they can slow application startup.

---

# Common Interview Traps

### Trap 1

❌ Bean definitions are actual bean objects.

✔ Bean definitions are metadata used to create bean instances later.

---

### Trap 2

❌ Component scanning creates objects immediately.

✔ It registers bean definitions. Instantiation occurs later during the refresh process.

---

### Trap 3

❌ Dependency injection happens before object creation.

✔ Spring must first instantiate the object, then inject its dependencies.

---

# Summary

Whenever you hear **`ApplicationContext.refresh()`**, think:

```text
Bean Definitions

↓

Bean Creation

↓

Dependency Injection

↓

Lifecycle

↓

Tomcat

↓

Application Ready
```

If you can explain this flow clearly in an interview, you'll demonstrate a solid understanding of Spring Boot internals rather than just surface-level usage.

---