# 01 - What is Spring?

> Part 2

---

# 3. History of Spring

Understanding **why Spring was created** is one of the most frequently asked conceptual interview topics. Many developers know how to use Spring but don't know the historical problems it solved.

Spring wasn't invented because Java was bad—it was invented because **enterprise Java development became unnecessarily complex**.

---

# Enterprise Java Before Spring

Let's travel back to the late 1990s and early 2000s.

Java was becoming the preferred language for enterprise applications because it offered:

- Platform independence ("Write Once, Run Anywhere")
- Strong Object-Oriented Programming support
- Automatic Garbage Collection
- Robust standard libraries
- Multi-threading capabilities

However, building enterprise applications required using **Java EE (formerly J2EE)**.

Typical enterprise applications included:

- Banking systems
- Insurance platforms
- E-commerce websites
- Airline reservation systems
- Telecom billing systems

While Java itself was powerful, Java EE introduced a lot of complexity.

---

# What Was Java EE?

Java EE (Enterprise Edition) was a collection of specifications for building enterprise applications.

It included APIs and technologies such as:

```
Java EE

├── Servlets
├── JSP
├── EJB
├── JMS
├── JTA
├── JPA (later)
├── JNDI
├── JDBC
└── JavaMail
```

The idea was good: standardize enterprise development.

The implementation, however, became increasingly difficult.

---

# The Biggest Problem: Enterprise JavaBeans (EJB)

One of the core technologies in Java EE was **Enterprise JavaBeans (EJB)**.

EJB promised features like:

- Transactions
- Security
- Remote method invocation
- Object lifecycle management
- Scalability

Sounds great, right?

The problem was **how you had to write EJBs**.

---

## Example: A Simple Business Class Using EJB

Suppose you only want to create a service that calculates a discount.

With early versions of EJB, your class had to:

- Implement framework interfaces
- Extend framework classes
- Throw framework exceptions
- Be deployed to a Java EE server
- Be configured using XML

Example (simplified):

```java
public class DiscountBean implements SessionBean {

    public void ejbCreate() {
    }

    public void ejbActivate() {
    }

    public void ejbPassivate() {
    }

    public void ejbRemove() {
    }

    public double calculateDiscount(double amount) {
        return amount * 0.10;
    }
}
```

Notice something?

The actual business logic is only **one method**.

Everything else exists because the framework requires it.

This violated one of the core software engineering principles:

> Your business logic should not depend heavily on infrastructure code.

---

# Heavyweight Containers

EJB applications had to run inside an **EJB Container** provided by an application server.

Examples:

- IBM WebSphere
- BEA WebLogic
- Oracle Application Server
- JBoss

The architecture looked like this:

```
Application

↓

EJB Components

↓

EJB Container

↓

Application Server

↓

Operating System
```

Developers couldn't simply run the application from their IDE.

They often had to:

- Package EAR files
- Deploy to an application server
- Wait for startup
- Test changes
- Repeat

This slowed development significantly.

---

# XML Configuration Explosion

Configuration was another major challenge.

A simple application could contain numerous XML files:

```
web.xml

ejb-jar.xml

application.xml

server.xml

datasource.xml

security.xml

persistence.xml
```

A large enterprise project might have tens of thousands of lines of XML.

Finding configuration errors became a tedious task.

---

# Tight Coupling to the Framework

Business classes became tightly coupled to EJB APIs.

Instead of writing simple Java classes, developers had to:

- Implement framework interfaces
- Extend framework classes
- Use server-specific APIs
- Deploy to compatible application servers

This made migration difficult and reduced portability.

---

# Poor Testability

Testing EJB components was cumbersome.

Many business classes required an EJB container to function correctly.

Developers often couldn't write simple unit tests because their code depended on:

- Application servers
- JNDI lookups
- Container-managed transactions
- Remote interfaces

Modern Spring applications allow you to instantiate and test most business classes directly.

---

# Development Cycle Was Slow

A typical workflow looked like this:

```
Write Code

↓

Compile

↓

Package EAR

↓

Deploy

↓

Restart Server

↓

Wait

↓

Test

↓

Fix Bug

↓

Repeat
```

Server startup could take several minutes.

Today, with Spring Boot DevTools, changes can often be reflected in seconds.

---

# Enter Rod Johnson

In 2002, software engineer **Rod Johnson** published a book:

> *Expert One-on-One J2EE Design and Development*

The book argued that many Java EE applications had become overly complicated.

Rod demonstrated that enterprise applications could be built using:

- Plain Java Objects (POJOs)
- Dependency Injection
- Interfaces
- Loose coupling
- Simpler configuration

The sample framework from the book evolved into what became the **Spring Framework**.

---

# The Philosophy Behind Spring

Spring wasn't trying to replace Java.

It aimed to simplify enterprise Java development.

Its guiding principles included:

- Simplicity over complexity
- Composition over inheritance
- Interfaces over concrete implementations
- POJOs over framework-dependent classes
- Testability by design
- Loose coupling through Dependency Injection

These principles remain central to Spring today.

---

# POJO: Plain Old Java Object

One of Spring's revolutionary ideas was that business classes should remain ordinary Java classes.

Example:

```java
public class PaymentService {

    public void processPayment() {
        System.out.println("Processing payment...");
    }
}
```

No framework interfaces.

No special base classes.

No container-specific code.

This made code:

- Easier to understand
- Easier to test
- Easier to maintain
- More reusable

Spring later manages these POJOs as beans inside its IoC container.

---

# Why Developers Loved Spring

Spring addressed the pain points of enterprise Java:

| Before Spring | With Spring |
|---------------|-------------|
| Heavy EJB components | Lightweight POJOs |
| Manual dependency wiring | Dependency Injection |
| Complex XML | Java-based configuration (later annotations) |
| Difficult unit testing | Easy testing with mocks |
| Tight framework coupling | Loose coupling |
| Heavy application servers | Lightweight containers |

---

# How Spring Changed Enterprise Development

Instead of forcing developers to adapt to the framework, Spring adapted to the developer.

Traditional approach:

```
Business Code

↓

Framework Requirements

↓

Infrastructure
```

Spring approach:

```
Business Code

↓

Spring Framework

↓

Infrastructure
```

Spring handled the infrastructure concerns behind the scenes.

---

# Spring's Guiding Principles

Spring emphasizes:

1. **Simplicity**
2. **Loose Coupling**
3. **Dependency Injection**
4. **Modularity**
5. **Testability**
6. **Reusability**
7. **Maintainability**

These principles influence nearly every feature in the framework.

---

# Interview Questions

## Basic

### Q1. Why was Spring created?

**Answer:**

Spring was created to simplify enterprise Java development by reducing the complexity of Java EE technologies like EJB. It introduced POJOs, Dependency Injection, and an IoC container, making applications easier to build, test, and maintain.

---

### Q2. Who created Spring?

**Answer:**

Rod Johnson introduced the ideas behind Spring in his 2002 book *Expert One-on-One J2EE Design and Development*. The framework evolved from the sample code presented in that book.

---

### Q3. What problems did Spring solve?

- Reduced EJB complexity
- Simplified object creation
- Eliminated much boilerplate code
- Improved testability
- Encouraged loose coupling
- Simplified configuration

---

## Intermediate

### Q4. Why were EJBs considered heavyweight?

Because they required:

- Application servers
- Framework interfaces
- XML configuration
- Container-managed lifecycle
- Remote interfaces
- Complex deployment

---

### Q5. What is a POJO, and why is it important in Spring?

A POJO (Plain Old Java Object) is a regular Java class without framework-specific inheritance or interfaces. Spring manages POJOs as beans, allowing business logic to remain independent of the framework, improving maintainability and testability.

---

# Summary

In this part, we covered:

- The state of enterprise Java before Spring
- Challenges with Java EE and EJB
- The motivation behind Spring
- Rod Johnson's contribution
- The importance of POJOs
- Spring's design philosophy
- Why Spring became widely adopted

---

## Next Part (Part 3)

We'll dive into the **Spring Architecture Overview**, including:

- High-level architecture
- Core modules
- Spring Container
- IoC Container introduction
- BeanFactory vs ApplicationContext (overview)
- How Spring processes an application from startup
- Internal execution flow
- ASCII architecture diagrams
- Production use cases
- Best practices