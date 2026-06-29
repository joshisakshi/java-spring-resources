---

# Chapter 4 — Starter Dependencies

> **Interview Importance:** ⭐⭐⭐⭐⭐

One of the biggest reasons behind Spring Boot's popularity is **Starter Dependencies**.

Every interview eventually asks one of these questions:

- What are Starter Dependencies?
- Why were they introduced?
- What is `spring-boot-starter-web`?
- What is the Spring Boot Parent POM?
- What is BOM (Bill Of Materials)?
- How does Spring Boot manage dependency versions?

This chapter answers all of these.

---

# Goal

By the end of this chapter, you should understand:

- Why Starter Dependencies exist
- How they reduce dependency management
- Parent POM
- BOM (Bill of Materials)
- Transitive Dependencies
- Most commonly used starters
- Best practices
- Production usage

---

# The Problem Before Starter Dependencies

Let's go back to traditional Spring.

Suppose you wanted to create a REST application.

Your `pom.xml` would look something like:

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-web</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
</dependency>

<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
</dependency>

<dependency>
    <groupId>jakarta.validation</groupId>
    <artifactId>jakarta.validation-api</artifactId>
</dependency>

<dependency>
    <groupId>org.apache.tomcat.embed</groupId>
    <artifactId>tomcat-embed-core</artifactId>
</dependency>
```

Even for a basic REST application, many dependencies had to be added manually.

---

# Another Problem — Version Compatibility

Imagine this setup:

```
Spring MVC 6.1

↓

Jackson 2.9

↓

Tomcat 11

↓

Validation 3.1
```

Not every combination works.

Sometimes upgrading one dependency broke another.

Developers spent considerable time resolving dependency conflicts.

---

# Enter Starter Dependencies

Spring Boot introduced the concept of **Starter Dependencies**.

Instead of adding ten individual libraries, you add one dependency.

Example:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

That's all.

Spring Boot downloads everything required for building a web application.

---

# What is a Starter?

**Interview Definition**

> A Starter Dependency is a curated collection of compatible libraries bundled together for a specific purpose, such as building web applications, REST APIs, data access, security, or testing.

Notice the important words:

- Curated
- Compatible
- Purpose-specific

Those are interview keywords.

---

# Internal Working

Suppose we add:

```xml
spring-boot-starter-web
```

Internally, Maven resolves something similar to:

```text
spring-boot-starter-web

↓

spring-boot-starter

↓

spring-core

↓

spring-context

↓

spring-web

↓

spring-webmvc

↓

jackson-databind

↓

tomcat-embed-core

↓

tomcat-embed-websocket

↓

spring-boot-autoconfigure

↓

spring-boot
```

You only declared one dependency.

Maven downloaded the complete dependency graph.

This happens because of **Transitive Dependencies**.

---

# What Are Transitive Dependencies?

Suppose:

```
Project

↓

Library A

↓

Library B

↓

Library C
```

If your project depends on **Library A**, Maven automatically downloads B and C as well.

This feature is called **Transitive Dependency Resolution**.

Spring Boot takes advantage of this to package commonly used libraries into starter modules.

---

# Most Common Starter Dependencies

These are the ones you should know for interviews.

| Starter | Purpose |
|----------|---------|
| `spring-boot-starter-web` | REST APIs, Spring MVC, Embedded Tomcat |
| `spring-boot-starter-data-jpa` | Hibernate, JPA |
| `spring-boot-starter-security` | Authentication & Authorization |
| `spring-boot-starter-validation` | Bean Validation |
| `spring-boot-starter-test` | JUnit, Mockito, Spring Test |
| `spring-boot-starter-actuator` | Monitoring & Health Checks |
| `spring-boot-starter-cache` | Caching Support |
| `spring-boot-starter-webflux` | Reactive Applications |

These are by far the most frequently used starters in production.

---

# Deep Dive — `spring-boot-starter-web`

This is probably the first dependency you'll add in any backend project.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

Internally, it brings in:

```text
Spring MVC

↓

Embedded Tomcat

↓

Jackson

↓

Spring Core

↓

Logging

↓

Validation

↓

JSON Support

↓

HTTP Message Converters
```

Without this starter, your `@RestController` endpoints won't work because the MVC infrastructure won't be configured.

---

# Deep Dive — `spring-boot-starter-data-jpa`

Purpose:

```
JPA

↓

Hibernate

↓

EntityManager

↓

Transactions

↓

Repository Support
```

Adding this starter allows you to write:

```java
public interface ProductRepository
        extends JpaRepository<Product, Long> {

}
```

without manually configuring Hibernate.

---

# Deep Dive — `spring-boot-starter-security`

Provides:

```
Authentication

↓

Authorization

↓

Password Encoding

↓

Security Filter Chain

↓

CSRF Protection

↓

Session Management
```

We'll study this thoroughly in Module 5.

---

# Deep Dive — `spring-boot-starter-test`

One of the most useful starters.

It includes:

- JUnit 5
- Mockito
- Spring Test
- AssertJ
- MockMvc
- JSON testing utilities

Instead of adding each testing library separately, Boot bundles them into one starter.

---

# Production Example

Suppose we're building an e-commerce backend.

The `pom.xml` might contain only:

```xml
spring-boot-starter-web

spring-boot-starter-data-jpa

spring-boot-starter-security

spring-boot-starter-validation

spring-boot-starter-actuator

mysql-connector-j
```

From these few dependencies, Spring Boot builds a complete backend application.

---

# Best Practices

✅ Prefer Starter Dependencies over manually adding Spring modules.

✅ Avoid adding duplicate libraries already included by a starter.

✅ Understand what each starter brings into your project.

---

# Interview Questions

## Question

Why are Starter Dependencies useful?

**Answer:**

> They simplify dependency management by providing a curated set of compatible libraries for a specific purpose, reducing boilerplate and version conflicts.

---

## Question

What is `spring-boot-starter-web`?

**Answer:**

> It is a starter dependency that includes Spring MVC, Embedded Tomcat, Jackson, Validation, Logging, and other libraries required for building web and REST applications.

---

## Question

What are Transitive Dependencies?

**Answer:**

> Transitive dependencies are libraries automatically downloaded by Maven because they are required by another dependency in the dependency graph.

---

# Common Interview Trap

❌ A Starter Dependency is a library.

✔ A Starter Dependency is a **collection of compatible libraries** bundled together for a specific use case.

---

# Coming Next

We'll now study something every senior backend developer must understand:

# Parent POM & BOM (Bill of Materials)

We'll answer:

- What is `spring-boot-starter-parent`?
- Why don't we specify versions for Spring Boot dependencies?
- What is BOM?
- How does Spring Boot ensure all libraries are version compatible?
- How does Maven resolve dependency versions internally?

This is another favorite interview topic because it combines Maven fundamentals with Spring Boot internals.

---

# Chapter 4 (Continued)

# Parent POM & BOM (Bill of Materials)

> **Interview Importance:** ⭐⭐⭐⭐☆

This topic is less about Spring itself and more about **how Spring Boot manages dependencies**.

Many developers can use Spring Boot for years without understanding why this works:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

Notice something missing?

There is **no version**.

Why?

Where does Maven get the version from?

This chapter answers exactly that.

---

# Goal

By the end of this section you should understand:

- Parent POM
- BOM (Bill Of Materials)
- Dependency Management
- Version Resolution
- Why versions are omitted
- Internal Maven dependency resolution
- Interview questions

---

# The Problem Before Spring Boot

Suppose we wanted these libraries:

```text
Spring MVC

Hibernate

Jackson

Tomcat

JUnit

Mockito
```

Normally we'd specify versions manually:

```xml
<dependency>
    <artifactId>spring-webmvc</artifactId>
    <version>6.2.4</version>
</dependency>

<dependency>
    <artifactId>hibernate-core</artifactId>
    <version>6.6.8</version>
</dependency>

<dependency>
    <artifactId>jackson-databind</artifactId>
    <version>2.18.1</version>
</dependency>
```

Imagine maintaining 100+ dependencies.

Every upgrade becomes painful.

---

# Version Compatibility Problem

Example:

```
Spring MVC

↓

Jackson

↓

Hibernate

↓

Tomcat

↓

SLF4J

↓

Logback
```

If you update Jackson,

you might also need to update:

- Spring MVC
- Hibernate
- Spring Boot

Keeping compatible versions manually is difficult.

---

# Spring Boot's Solution

Spring Boot manages dependency versions for you.

You usually write:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

No version.

Spring Boot decides it.

---

# Parent POM

Most Spring Boot projects begin like this:

```xml
<parent>

    <groupId>org.springframework.boot</groupId>

    <artifactId>
        spring-boot-starter-parent
    </artifactId>

    <version>3.x.x</version>

</parent>
```

This parent POM provides:

- Default plugin configuration
- Dependency versions
- Java version defaults
- Build configuration
- Maven best practices

Think of it as a predefined project template.

---

# Real-World Analogy

Imagine a university.

Instead of every professor creating:

- Exam rules
- Grading rules
- Attendance policy

individually,

the university provides common policies.

Each department simply inherits them.

Similarly,

your project inherits common Maven configuration from:

```
spring-boot-starter-parent
```

---

# What is a BOM?

BOM stands for:

> **Bill Of Materials**

Interview Definition:

> A BOM is a centralized list of dependency versions used to ensure all related libraries remain compatible.

Notice:

A BOM **does not download libraries**.

It only manages versions.

---

# Visual Representation

```
Spring Boot BOM

│

├── Spring Framework

├── Jackson

├── Hibernate

├── Tomcat

├── Logback

├── SLF4J

├── JUnit

└── Mockito
```

Each library has a version chosen and tested by the Spring Boot team.

---

# How Does Maven Use the BOM?

Suppose you write:

```xml
<dependency>

    <groupId>org.springframework.boot</groupId>

    <artifactId>
        spring-boot-starter-web
    </artifactId>

</dependency>
```

Maven asks:

```
Version Missing

↓

Check Dependency Management

↓

Found in Parent POM

↓

Download Correct Version
```

This happens automatically.

---

# Dependency Resolution Flow

```text
pom.xml

↓

Parent POM

↓

Dependency Management

↓

BOM

↓

Resolve Versions

↓

Download Libraries
```

This is the complete dependency resolution pipeline.

---

# Dependency Management vs Dependencies

This distinction is frequently asked.

## `<dependencies>`

Actually downloads libraries.

Example:

```xml
<dependencies>

    <dependency>
        ...
    </dependency>

</dependencies>
```

---

## `<dependencyManagement>`

Only manages versions.

It does **not** download anything.

Think of it as:

```
Version Catalog
```

---

# Internal Picture

```
dependencyManagement

↓

Stores Versions

↓

dependencies

↓

Uses Versions

↓

Downloads Libraries
```

This separation is important.

---

# Example

Suppose the BOM says:

```text
Jackson

↓

2.18.1
```

Now you write:

```xml
<dependency>

    <groupId>
        com.fasterxml.jackson.core
    </groupId>

    <artifactId>
        jackson-databind
    </artifactId>

</dependency>
```

No version required.

Maven automatically uses:

```
2.18.1
```

---

# Can We Override Versions?

Yes.

Suppose you need a newer version.

You can specify:

```xml
<dependency>

    <artifactId>
        jackson-databind
    </artifactId>

    <version>2.19.0</version>

</dependency>
```

Your explicit version overrides the BOM.

However...

---

# Best Practice

Avoid overriding Spring Boot managed versions unless:

- fixing a security vulnerability
- a critical bug fix is required
- the Spring Boot release notes recommend it

Otherwise, let Spring Boot manage compatible versions.

---

# Parent POM Responsibilities

Besides dependency versions, it also configures:

- Maven Compiler Plugin
- Spring Boot Maven Plugin
- Resource filtering
- UTF-8 encoding
- Default Java version
- Test plugins

This reduces project boilerplate even further.

---

# Interview Perspective

## Question

Why don't we specify versions for Spring Boot starters?

**Excellent Answer:**

> Spring Boot manages dependency versions through its Parent POM and BOM. Maven retrieves the correct compatible version from dependency management, so developers usually don't need to specify versions manually.

---

## Question

What is a BOM?

**Excellent Answer:**

> BOM stands for Bill Of Materials. It is a centralized dependency version catalog that ensures all Spring Boot libraries and related dependencies use compatible versions.

---

## Question

Does a BOM download dependencies?

**Answer:**

No.

It only manages versions.

Dependencies are downloaded only when declared under:

```xml
<dependencies>
```

---

## Question

Difference between Dependency Management and Dependencies?

| Dependency Management | Dependencies |
|-----------------------|--------------|
| Manages versions | Downloads libraries |
| No JAR download | Downloads JARs |
| Version catalog | Actual project dependencies |

---

# Production Example

Our e-commerce backend contains:

```text
spring-boot-starter-web

spring-boot-starter-data-jpa

spring-boot-starter-security

spring-boot-starter-validation
```

We don't specify versions.

The Parent POM and BOM ensure:

- Spring MVC works with Hibernate.
- Jackson is compatible with Spring.
- Embedded Tomcat matches the Spring Boot release.
- Logging libraries are compatible.

This greatly reduces maintenance effort and prevents many runtime issues.

---

# Common Interview Traps

### Trap 1

❌ Parent POM and BOM are the same thing.

✔ The Parent POM **uses** a BOM to provide dependency management, along with plugin configuration and other project defaults.

---

### Trap 2

❌ BOM downloads dependencies.

✔ BOM only provides versions.

---

### Trap 3

❌ We should always override managed versions.

✔ In most cases, let Spring Boot manage versions. Override only when necessary.

---

# Summary

Remember this flow:

```text
Parent POM

↓

Dependency Management

↓

BOM

↓

Resolve Versions

↓

Dependencies

↓

Download Libraries
```

If someone asks:

> **"Why don't Spring Boot dependencies require versions?"**

You now know the complete answer.

---

# Coming Next

Now we'll begin one of the **most important and frequently asked topics in Spring Boot interviews**:

# Chapter 5 — Auto Configuration

We'll answer:

- What is Auto Configuration?
- How does Spring Boot decide which beans to create?
- What is `AutoConfiguration.imports`?
- What are `@ConditionalOnClass`, `@ConditionalOnBean`, `@ConditionalOnMissingBean`, and `@ConditionalOnProperty`?
- How does Spring Boot avoid creating unnecessary beans?

This is arguably the **most valuable chapter in Module 2** because it explains the "magic" behind Spring Boot.

---

# Chapter 5 — Auto Configuration

> **Interview Importance:** ⭐⭐⭐⭐⭐ (One of the Highest Frequency Spring Boot Topics)

If Spring Boot has one feature that feels like "magic," it's **Auto Configuration**.

You add a dependency:

```xml
spring-boot-starter-data-jpa
```

Run the application.

Suddenly:

- `DataSource` exists.
- `EntityManager` exists.
- `TransactionManager` exists.
- Hibernate works.

You never wrote:

```java
@Bean
```

So...

**Who created these beans?**

The answer is **Auto Configuration**.

---

# Goal

By the end of this chapter, you should understand:

- What Auto Configuration is
- Why it exists
- How Spring Boot decides what to configure
- Why it doesn't configure unnecessary beans
- The internal startup flow
- Interview questions

---

# What is Auto Configuration?

## Interview Definition

> Auto Configuration is a Spring Boot feature that automatically configures infrastructure beans based on the application's classpath, existing beans, and configuration properties.

Notice the wording carefully.

It does **not** configure everything.

It configures **only what is appropriate** for your application.

---

# The Problem Before Auto Configuration

Suppose you wanted to use Spring MVC.

Without Boot, you had to configure:

- DispatcherServlet
- ViewResolver
- HandlerMapping
- MessageConverters
- ObjectMapper
- MultipartResolver

Similarly, for JPA:

- DataSource
- EntityManagerFactory
- TransactionManager
- Hibernate Vendor Adapter

Every project repeated the same configuration.

Most of it was identical.

---

# Spring Boot's Idea

Spring Boot asks a simple question:

> **"Can I safely create this bean for the developer?"**

If the answer is **yes**, it creates it automatically.

If not, it leaves it alone.

This dramatically reduces boilerplate.

---

# Important Principle

Spring Boot **never blindly creates beans**.

Instead, it checks:

```text
Is required library present?

↓

Is configuration available?

↓

Has the developer already defined a bean?

↓

If all conditions pass

↓

Create Bean
```

This conditional behavior is the core of Auto Configuration.

---

# Real Example

Suppose your project contains:

```xml
spring-boot-starter-web
```

Spring Boot sees:

```text
Spring MVC available

↓

Servlet API available

↓

Tomcat available

↓

Create DispatcherServlet

↓

Create RequestMappingHandlerMapping

↓

Create MessageConverters

↓

Create ObjectMapper

↓

Application Ready
```

You didn't create any of these beans yourself.

---

# Another Example

Add:

```xml
spring-boot-starter-data-jpa
```

And configure:

```properties
spring.datasource.url=...
```

Spring Boot detects:

```text
Hibernate Present

↓

Datasource Configured

↓

Create DataSource

↓

Create EntityManagerFactory

↓

Create TransactionManager

↓

Create JpaRepositories
```

Again, no manual configuration required.

---

# User Beans vs Framework Beans

One of the most common interview questions.

Spring Boot creates:

```text
Infrastructure Beans

↓

DispatcherServlet

↓

ObjectMapper

↓

DataSource

↓

TransactionManager

↓

Validator
```

You create:

```text
ProductController

↓

ProductService

↓

ProductRepository
```

Together they form a complete application.

---

# Startup Flow

```text
SpringApplication.run()

↓

Component Scan

↓

Register User Beans

↓

Run Auto Configuration

↓

Register Framework Beans

↓

Create All Beans

↓

Dependency Injection

↓

Application Ready
```

This is the mental model you should always remember.

---

# Does Auto Configuration Override My Beans?

**No.**

This is a design principle of Spring Boot.

If you've already defined your own bean, Spring Boot usually backs off.

For example:

```java
@Bean
public ObjectMapper objectMapper() {
    return new ObjectMapper();
}
```

Spring Boot detects your bean and typically avoids creating another one.

This behavior is enabled by conditional annotations, which we'll study next.

---

# Summary

Think of Auto Configuration as an intelligent assistant.

It asks:

```text
Can I configure this safely?

↓

Yes

↓

Create Bean

↓

No

↓

Skip It
```

This simple idea is what makes Spring Boot feel effortless while still remaining flexible.

---

# Coming Next

In the next section, we'll go **under the hood** and study:

- `AutoConfiguration.imports`
- `@EnableAutoConfiguration`
- `@Import`
- `@ConditionalOnClass`
- `@ConditionalOnBean`
- `@ConditionalOnMissingBean`
- `@ConditionalOnProperty`

This is where we'll uncover how Spring Boot actually performs Auto Configuration internally.

---

# Chapter 6–10 — Configuration, Profiles, @ConfigurationProperties, Embedded Tomcat & Actuator

> **Interview Importance**
>
> - application.properties / application.yml ⭐⭐⭐⭐☆
> - Profiles ⭐⭐⭐⭐☆
> - @ConfigurationProperties ⭐⭐⭐⭐☆
> - Embedded Tomcat ⭐⭐⭐☆☆
> - Actuator ⭐⭐⭐☆☆

---

# Goal

This chapter covers the remaining Spring Boot features that every backend developer should know.

Instead of documentation-level details, we focus on:

- Interview questions
- Internal working
- Production usage
- Best practices
- Common mistakes

---

# 1. application.properties vs application.yml

## Why do we need configuration files?

Applications should not hardcode values like:

- Database URL
- Passwords
- API Keys
- Ports
- Timeouts

Bad

```java
String url = "jdbc:mysql://localhost:3306/shop";
```

Good

```properties
spring.datasource.url=...
```

Spring reads configuration from external files.

This follows the **Externalized Configuration** principle.

---

## application.properties

Simple key-value format.

```properties
server.port=8081

spring.datasource.url=jdbc:mysql://localhost:3306/shop

spring.datasource.username=root

spring.jpa.hibernate.ddl-auto=update
```

Easy to read.

Ideal for smaller projects.

---

## application.yml

Hierarchical format.

```yaml
server:
  port: 8081

spring:
  datasource:
    url: jdbc:mysql://localhost:3306/shop
    username: root
```

Cleaner for nested configurations.

Most modern Spring Boot projects prefer YAML.

---

## Internal Working

During startup:

```text
SpringApplication.run()

↓

Prepare Environment

↓

Read Properties

↓

Merge Configuration Sources

↓

Store in Environment

↓

Beans Read Values
```

When you write:

```java
@Value("${server.port}")
```

Spring fetches it from the Environment object.

---

## Interview Questions

### Which is better?

Neither.

Both are fully supported.

YAML is more readable for nested structures.

Properties are simpler.

---

# 2. Profiles

Profiles allow different configurations for different environments.

Example:

```
Development

↓

Testing

↓

Staging

↓

Production
```

Instead of changing one file repeatedly:

Use multiple configuration files.

```
application-dev.yml

application-test.yml

application-prod.yml
```

Activate profile:

```properties
spring.profiles.active=dev
```

---

## Real Production Example

Development

```
MySQL running locally

Debug Logs

Mock APIs
```

Production

```
Cloud Database

INFO Logs

Real APIs

SSL Enabled
```

Same code.

Different configuration.

---

## Interview Question

Why Profiles?

Answer:

> Profiles allow environment-specific configuration without changing application code.

---

# 3. @ConfigurationProperties

Suppose:

```yaml
payment:
  timeout: 30
  retries: 5
```

Bad approach:

```java
@Value("${payment.timeout}")

@Value("${payment.retries}")
```

Good approach:

```java
@ConfigurationProperties(prefix = "payment")
```

Now Spring maps everything into one POJO.

Conceptually:

```
application.yml

↓

Environment

↓

@ConfigurationProperties

↓

Java Object
```

Benefits:

- Type Safety
- Cleaner Code
- Easier Testing
- Validation Support

Interview Tip:

Prefer `@ConfigurationProperties` over many `@Value` annotations.

---

# 4. Embedded Tomcat

Before Spring Boot:

```
Build WAR

↓

Deploy to External Tomcat

↓

Restart Server
```

Spring Boot:

```
Run Application

↓

Embedded Tomcat Starts

↓

Ready
```

Startup:

```
SpringApplication.run()

↓

ApplicationContext

↓

Tomcat Created

↓

DispatcherServlet Registered

↓

Port Open

↓

Ready
```

Default Port

```
8080
```

Change Port

```properties
server.port=9090
```

---

## Why Embedded Server?

- Easier Deployment
- Single Executable JAR
- No External Installation
- Better Microservices Support

---

# 5. Spring Boot Actuator

Actuator provides production monitoring.

Examples:

- Health
- Metrics
- Beans
- Environment
- Info

Common endpoint:

```
/actuator/health
```

Response:

```json
{
  "status": "UP"
}
```

Companies use Actuator with:

- Prometheus
- Grafana
- Kubernetes
- Cloud Monitoring

---

## Internal Working

Actuator registers additional endpoints as Spring Beans.

Request Flow:

```
Browser

↓

Tomcat

↓

DispatcherServlet

↓

Actuator Endpoint

↓

Health Information
```

---

# Production Usage

Almost every microservice enables:

- Health Checks
- Readiness Checks
- Liveness Checks

Kubernetes periodically calls:

```
/actuator/health
```

If unhealthy:

Container Restart.

---

# Best Practices

✅ Use YAML for larger projects.

✅ Use Profiles for every environment.

✅ Prefer `@ConfigurationProperties`.

✅ Never store passwords in Git.

✅ Expose only required Actuator endpoints.

✅ Secure Actuator endpoints using Spring Security.

---

# Common Mistakes

❌ Hardcoding database credentials.

❌ Using only one configuration for all environments.

❌ Exposing all Actuator endpoints publicly.

❌ Using dozens of `@Value` annotations.

❌ Committing production secrets to Git.

---

# Top Interview Questions

### What is the Environment object?

It stores configuration collected from:

- application.properties
- application.yml
- Environment Variables
- JVM Properties
- Command Line Arguments

---

### Difference between @Value and @ConfigurationProperties?

| @Value | @ConfigurationProperties |
|----------|--------------------------|
| One property | Multiple properties |
| String-based | Type-safe |
| Small configs | Large configs |

---

### Why Profiles?

Different configurations for different environments without changing code.

---

### Why Embedded Tomcat?

It packages the web server inside the application, enabling self-contained deployment.

---

### What is Spring Boot Actuator?

A production monitoring module that exposes operational endpoints such as health, metrics, info, and environment details.

---

# One-Page Revision

```
Configuration

↓

Environment

↓

Profiles

↓

@ConfigurationProperties

↓

ApplicationContext

↓

Embedded Tomcat

↓

DispatcherServlet

↓

REST APIs

↓

Actuator

↓

Monitoring
```

---

# Module 2 Summary

After completing Module 2, you should understand:

✓ Why Spring Boot was introduced

✓ Spring Boot Architecture

✓ @SpringBootApplication

✓ SpringApplication.run()

✓ ApplicationContext startup

✓ Starter Dependencies

✓ Parent POM & BOM

✓ Auto Configuration

✓ Configuration Files

✓ Profiles

✓ @ConfigurationProperties

✓ Embedded Tomcat

✓ Actuator

You now know how a Spring Boot application starts from:

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

Dependency Injection

↓

Embedded Tomcat

↓

DispatcherServlet

↓

Application Ready
```

This startup flow is one of the highest-value concepts for Spring Boot interviews and forms the foundation for Modules 3–8.

---