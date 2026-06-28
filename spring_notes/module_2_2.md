# Module 1 - Spring Fundamentals

# 02 - Problems Before Spring

> Part 2

---

# 3. Enterprise JavaBeans (EJB) – The Original Enterprise Solution

Before Spring existed, Java already had a framework for enterprise applications.

It was called **Enterprise JavaBeans (EJB)**.

At first glance, EJB looked like an excellent solution because it provided many enterprise features out of the box.

These included:

- Transaction Management
- Security
- Object Lifecycle
- Remote Method Invocation (RMI)
- Object Pooling
- Concurrency Management
- Persistence (later versions)

Large organizations such as banks, telecom companies, and insurance firms adopted EJB extensively.

So the obvious question is:

> If EJB already solved enterprise problems, why was Spring created?

To answer that, we first need to understand what EJB actually did.

---

# What Problem Was EJB Trying to Solve?

Imagine you're building an online banking application.

The business logic is actually quite small.

```
Transfer Money

↓

Update Balance

↓

Save Transaction

↓

Send Notification
```

However, a production banking application also needs:

- Authentication
- Authorization
- Database Transactions
- Logging
- Rollback
- Scalability
- Connection Pooling
- Thread Safety
- Remote Communication

These are called **cross-cutting concerns** because almost every business operation needs them.

Without a framework, developers had to implement these repeatedly.

EJB attempted to centralize these responsibilities.

---

# EJB Architecture

A simplified architecture looked like this:

```
                    Client

                      │

                      ▼

              Application Server

                      │

        ┌─────────────┴─────────────┐

        │                           │

        ▼                           ▼

   EJB Container              Other Services

        │

        ▼

   Enterprise Bean

        │

        ▼

     Database
```

Notice something important.

The application didn't directly create business objects.

Everything went through the **EJB Container**.

---

# What is an EJB Container?

The EJB Container was responsible for:

- Creating beans
- Destroying beans
- Managing transactions
- Security checks
- Remote communication
- Thread management
- Lifecycle callbacks

Conceptually:

```
Developer

↓

Business Logic

↓

EJB Container

↓

Infrastructure
```

This sounds familiar.

Later you'll notice Spring also has a container.

The difference lies in **how lightweight and flexible** that container is.

---

# Building an EJB Wasn't Simple

Suppose we want to create a simple payment service.

Today in Spring we write:

```java
@Service
public class PaymentService {

    public void processPayment() {

    }

}
```

Done.

In EJB 2.x, the same service required multiple artifacts.

---

# Step 1 – Remote Interface

```java
public interface PaymentService extends EJBObject {

    void processPayment()
            throws RemoteException;

}
```

Notice:

Your business interface now depends on EJB APIs.

---

# Step 2 – Home Interface

```java
public interface PaymentHome extends EJBHome {

    PaymentService create()
            throws RemoteException;

}
```

Another interface.

More framework code.

---

# Step 3 – Bean Class

```java
public class PaymentBean implements SessionBean {

    public void processPayment() {

    }

    public void ejbActivate() {}

    public void ejbPassivate() {}

    public void ejbRemove() {}

    public void setSessionContext(...) {}

}
```

How much business logic exists?

Almost none.

Most methods exist only because the framework requires them.

---

# Step 4 – XML Deployment Descriptor

```xml
<session>

    <ejb-name>
        PaymentBean
    </ejb-name>

    <home>
        PaymentHome
    </home>

    <remote>
        PaymentService
    </remote>

</session>
```

More configuration.

Still no business logic.

---

# Compare With Spring

Spring:

```java
@Service
public class PaymentService {

    public void processPayment() {

    }

}
```

That's it.

No interfaces.

No container APIs.

No lifecycle methods.

No deployment descriptors.

Your business class remains a normal Java class.

---

# Why Was This a Problem?

Notice how the framework started controlling your code.

Instead of writing:

```
Business Logic
```

Developers had to write:

```
Framework Code

↓

Business Logic

↓

Framework Code

↓

Framework Code

↓

Framework Code
```

The business logic became buried beneath infrastructure.

---

# Framework Pollution

One criticism of EJB was that it **polluted business classes**.

For example:

```java
public class PaymentBean
        implements SessionBean
```

Now your business class cannot exist independently.

It depends directly on the framework.

Compare that to Spring:

```java
public class PaymentService {

}
```

This is just a normal Java class.

Spring calls such classes **POJOs (Plain Old Java Objects).**

We'll study POJOs in depth later.

---

# Why Framework Coupling Is Dangerous

Suppose your company decides to migrate away from EJB.

Every business class now contains:

- EJB interfaces
- Lifecycle methods
- Remote exceptions
- Container APIs

Migration becomes expensive.

This is called **framework coupling**.

Spring's philosophy is different.

Your business code should know as little as possible about the framework.

---

# Interview Insight

One of Spring's biggest innovations wasn't Dependency Injection.

It was allowing developers to write **business classes that remain ordinary Java classes.**

This dramatically improved:

- Portability
- Testability
- Readability
- Maintainability

---

# EJB Wasn't "Bad"

This is important.

Many developers unfairly criticize EJB.

In reality:

At the time it was introduced, EJB solved genuine enterprise problems.

Remember:

Early 2000s infrastructure was very different.

Cloud computing didn't exist.

Docker didn't exist.

Spring didn't exist.

Microservices didn't exist.

Application servers were the standard deployment model.

EJB provided valuable capabilities for that ecosystem.

The issue wasn't the ideas.

The issue was the developer experience.

---

# Comparison: EJB vs Spring

| Feature | EJB 2.x | Spring |
|----------|----------|---------|
| Business Classes | Framework-dependent | Plain Java Objects (POJOs) |
| XML Configuration | Extensive | Minimal (later annotations) |
| Testing | Difficult | Easy |
| Object Creation | Container-managed | IoC Container |
| Coupling | Tight | Loose |
| Application Server | Mandatory | Optional (Spring Boot embeds one) |
| Learning Curve | Steep | Gentler |
| Boilerplate | High | Low |

---

# Interview Questions

## Basic

### What was EJB?

Enterprise JavaBeans was a Java EE technology for building enterprise applications. It provided services like transaction management, security, lifecycle management, and remote communication through an EJB container.

---

### Why was EJB considered heavyweight?

Because it required:

- Multiple interfaces
- XML deployment descriptors
- Application servers
- Framework-specific APIs
- Container-managed lifecycle
- Significant boilerplate code

---

## Intermediate

### Why did Spring replace EJB in many projects?

Spring offered similar enterprise capabilities while allowing developers to write simple POJOs, reducing boilerplate, improving testability, and promoting loose coupling.

---

### What is framework coupling?

Framework coupling occurs when business code directly depends on framework-specific classes or interfaces, making the code harder to test, maintain, and migrate.

---

# Key Takeaways

- EJB attempted to solve enterprise challenges.
- The EJB Container managed object lifecycle and infrastructure.
- Developers had to write a large amount of framework code.
- Business classes became tightly coupled to EJB APIs.
- Spring adopted many of EJB's goals but implemented them with a much simpler programming model.
- Spring's emphasis on POJOs became one of its defining strengths.

---

## Next Part

We'll examine three major pain points that pushed developers toward Spring:

1. XML Configuration Hell
2. Tight Coupling
3. Difficult Unit Testing

These topics directly lead into why **Inversion of Control (IoC)** and **Dependency Injection (DI)** became revolutionary concepts.