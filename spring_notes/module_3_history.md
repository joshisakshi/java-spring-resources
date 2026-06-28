# Module 1 - Spring Fundamentals

# 03 - History of Spring

> Interview Focused

**Interview Frequency:** ⭐⭐☆☆☆ (Occasionally Asked)

> **Why are we studying this?**
>
> You are **not** expected to remember every version of Spring or every release date.
>
> You **are** expected to know:
>
> - Why Spring was created
> - Who created it
> - How it evolved
> - Why Spring Boot became necessary
> - Why modern companies use Spring Boot instead of plain Spring Framework

---

# Goal

After completing this chapter, you should be able to answer:

- Who created Spring?
- Why was Spring created?
- How did Spring evolve?
- Why did Spring Boot come into existence?
- What are the major milestones in Spring's evolution?
- Why is Spring still the dominant Java backend framework?

---

# Table of Contents

1. Before Spring
2. Birth of Spring
3. Rod Johnson
4. Spring 1.x
5. Spring 2.x
6. Spring 3.x
7. Spring Boot
8. Modern Spring
9. Why Spring Won
10. Interview Questions
11. Cheat Sheet

---

# 1. Before Spring (1998–2002)

Before Spring, Enterprise Java development looked like this:

```
Developer

↓

Servlets

↓

EJB

↓

Application Server

↓

Database
```

Everything revolved around Java EE.

The major enterprise solution was:

- Enterprise JavaBeans (EJB)

Although EJB solved many enterprise problems, it introduced several new ones:

- Huge XML files
- Difficult testing
- Heavy application servers
- Boilerplate code
- Tight framework coupling

Developers wanted something simpler.

---

# 2. Rod Johnson

Interview Frequency: ⭐⭐⭐☆☆

The Spring Framework was created by **Rod Johnson**.

He was an Australian software engineer and Java consultant.

While working on enterprise Java projects, he noticed a recurring problem:

> Developers spent more time configuring infrastructure than writing business logic.

He believed enterprise Java could be much simpler.

---

# The Book That Started It All

In 2002, Rod Johnson published:

**Expert One-on-One J2EE Design and Development**

Instead of just criticizing Java EE, the book contained working code that demonstrated a simpler approach using:

- Plain Java Objects (POJOs)
- Dependency Injection
- Interfaces
- Loose Coupling
- Testable Components

That sample code became the foundation of the Spring Framework.

> **Interview Tip:** If asked "How did Spring start?", mention both **Rod Johnson** and his 2002 book.

---

# 3. Spring 1.0 (2004)

Interview Frequency: ⭐⭐⭐☆☆

Spring Framework 1.0 was officially released in **2004**.

Its primary goals were:

- Simplify Enterprise Java
- Replace EJB where possible
- Promote POJOs
- Introduce Dependency Injection
- Reduce XML complexity

At this stage, Spring mainly provided:

```
Spring Core

↓

IoC Container

↓

Dependency Injection
```

These concepts became the foundation of modern Spring.

---

# Why Developers Loved Spring

Instead of writing:

```
EJB

↓

Container

↓

Framework Interfaces

↓

XML
```

Developers could now write:

```java
public class UserService {

}
```

Just a normal Java class.

This was revolutionary at the time.

---

# 4. Spring 2.x (2006)

Spring 2.x focused on improving developer productivity.

Major improvements included:

- Better XML configuration
- Simplified AOP
- Improved bean configuration
- Namespace support

Although XML was still widely used, configuration became much cleaner.

---

# 5. Spring 3.x (2009)

⭐⭐⭐⭐☆

This version changed Spring development forever.

Why?

Because annotations became mainstream.

Instead of XML:

```xml
<bean id="userService"
      class="com.example.UserService"/>
```

Developers could write:

```java
@Service
public class UserService {

}
```

Other important annotations also became common:

```java
@Component

@Repository

@Controller

@Autowired

@Configuration
```

This dramatically reduced configuration.

---

# Java Configuration

Spring also introduced Java-based configuration.

Instead of XML:

```xml
<bean .../>
```

Developers could write:

```java
@Configuration

public class AppConfig {

    @Bean

    public UserService userService() {

        return new UserService();

    }

}
```

This was the beginning of "Configuration as Code."

---

# 6. The Rise of Spring MVC

Around the same period, Spring MVC gained popularity.

Request flow:

```
Browser

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

This architecture is still used in modern Spring Boot applications.

---

# 7. Spring Boot (2014)

Interview Frequency: ⭐⭐⭐⭐⭐

This is one of the most commonly asked conceptual questions.

Interviewer:

> Why was Spring Boot introduced?

Expected Answer:

Although Spring simplified enterprise development, developers still had to configure:

- Tomcat
- DispatcherServlet
- Dependencies
- XML/Java Config
- DataSource
- Logging
- Build files

Creating a new project still required significant setup.

Spring Boot solved this.

---

# Spring Before Boot

Creating a REST API involved:

- Configure Tomcat
- Configure MVC
- Configure DispatcherServlet
- Configure Context
- Configure Beans
- Configure Jackson
- Configure Logging

Lots of boilerplate.

---

# Spring Boot

Create a project.

Add dependency.

Write:

```java
@RestController

public class UserController {

}
```

Run:

```java
@SpringBootApplication

public class Application {

    public static void main(String[] args) {

        SpringApplication.run(Application.class,args);

    }

}
```

Done.

Embedded Tomcat starts automatically.

---

# Why Spring Boot Became Popular

Spring Boot introduced:

- Auto Configuration
- Starter Dependencies
- Embedded Tomcat
- Opinionated Defaults
- Production-ready features
- Actuator
- Metrics
- Health Checks

Developers could focus almost entirely on business logic.

---

# 8. Spring Today

Modern Spring is much more than Dependency Injection.

It provides:

```
Spring

│

├── Core

├── MVC

├── Data JPA

├── Security

├── Boot

├── Cloud

├── Batch

├── Kafka

├── Redis

├── Reactive

└── Testing
```

This ecosystem makes Spring suitable for almost every backend use case.

---

# Why Spring Won

Several factors contributed to Spring's widespread adoption.

## 1. Simplicity

Developers write ordinary Java classes.

---

## 2. Loose Coupling

Dependency Injection made applications easier to maintain.

---

## 3. Testability

Services could be unit tested without an application server.

---

## 4. Modular Design

Use only what you need.

---

## 5. Strong Community

Extensive documentation, tutorials, and community support.

---

## 6. Enterprise Ready

Spring scales from small applications to large distributed systems.

---

## 7. Continuous Evolution

Instead of becoming obsolete, Spring adapted to:

- Cloud Computing
- Containers
- Kubernetes
- Microservices
- Reactive Programming
- Native Images

---

# Timeline

```
1998

↓

Java EE

↓

EJB

↓

2002

↓

Rod Johnson's Book

↓

2004

↓

Spring 1.0

↓

2006

↓

Spring 2.x

↓

2009

↓

Spring 3.x

↓

Annotations

↓

2014

↓

Spring Boot

↓

Today

↓

Cloud

Microservices

Reactive

Kubernetes
```

---

# Common Interview Questions

## Q1. Who created Spring?

**Answer:**

Rod Johnson.

---

## Q2. Why was Spring created?

To simplify enterprise Java development by reducing the complexity of Java EE and EJB while promoting POJOs, Dependency Injection, and loose coupling.

---

## Q3. Why was Spring Boot introduced?

Spring Boot was introduced to eliminate repetitive configuration by providing auto-configuration, starter dependencies, embedded servers, and production-ready defaults.

---

## Q4. Does Spring Boot replace Spring Framework?

**No.**

Spring Boot is built **on top of** Spring Framework.

Think of it like this:

```
Spring Framework

↓

Foundation

↓

Spring Boot

↓

Convenience Layer
```

---

# Interview Trap

### Interviewer:

"Is Spring Boot a different framework?"

Wrong Answer:

> Yes.

Correct Answer:

> No. Spring Boot is an extension of Spring Framework that simplifies configuration and project setup using conventions, auto-configuration, and starter dependencies.

---

# Real Project Perspective

Imagine joining a company like BitGo.

When you clone a service and run:

```bash
./mvnw spring-boot:run
```

or

```bash
./gradlew bootRun
```

Within seconds:

- Embedded Tomcat starts
- Beans are created
- Dependencies are injected
- Database connections are initialized
- REST APIs are ready

This smooth experience is possible because of Spring Boot.

---

# Summary

You should now know:

- Why Spring was created
- Who created it
- How it evolved
- Why annotations became important
- Why Spring Boot was introduced
- Why Spring remains the dominant Java backend framework

> **Remember:** Interviews rarely focus on version numbers. Focus on the **motivation behind each evolution**, especially the transition from Java EE → Spring → Spring Boot.

---

# Cheat Sheet

```
Java EE

↓

Complex

↓

EJB

↓

Heavyweight

↓

Rod Johnson (2002)

↓

Spring (2004)

↓

POJOs

DI

IoC

↓

Annotations

↓

Spring Boot (2014)

↓

Auto Configuration

Embedded Server

Starter Dependencies

↓

Modern Backend Development
```

---

# Exercises

## Conceptual

1. Explain why Spring was created in under two minutes.
2. Explain why Spring Boot was introduced.
3. Compare Java EE, Spring Framework, and Spring Boot.

## Interview Practice

**Q:** Why do companies prefer Spring Boot over plain Spring Framework today?

Try answering without mentioning "less code." Focus on:
- Developer productivity
- Convention over configuration
- Faster onboarding
- Production readiness
- Reduced configuration errors

---

# End of Chapter

**Next Chapter:** `04 - Spring Architecture`

⭐ **Interview Frequency:** ★★★★★

This is where the real internals begin.

We'll cover:

- Core Container
- BeanFactory
- ApplicationContext
- Spring Modules
- How modules interact
- Internal architecture
- Execution flow
- Where IoC fits in
- High-level request processing

This chapter is the foundation for everything that follows.