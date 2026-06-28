# Module 1 - Spring Fundamentals

# 02 - Problems Before Spring

> Part 1

---

# Goal

In the previous chapter, we learned **what Spring is**.

Now we're going to understand **why Spring became necessary**.

This chapter is extremely important because many interviewers don't ask:

> "What is Spring?"

Instead they ask

> "Why was Spring created?"

or

> "What problems did Spring solve?"

To answer these properly, we need to travel back to the early days of Enterprise Java.

After completing this chapter you should be able to answer:

- Why was Java EE difficult?
- What were Enterprise JavaBeans (EJB)?
- Why was XML everywhere?
- Why was object creation painful?
- What is tight coupling?
- Why did Dependency Injection become revolutionary?
- Why was Spring considered lightweight?

---

# Table of Contents

1. Enterprise Java Before Spring
2. The Rise of Java EE
3. Problems with Enterprise Development
4. Heavyweight Frameworks
5. Tight Coupling
6. Boilerplate Code
7. Difficult Testing
8. XML Hell
9. Vendor Lock-in
10. Performance Problems
11. Summary

---

# 1. Enterprise Java Before Spring

To appreciate Spring, imagine you're a Java developer in the year **2001**.

There is no:

- Spring Boot
- Spring Framework
- Maven
- Gradle
- Embedded Tomcat
- Auto Configuration
- Dependency Injection

If someone asks you to build an online banking application,

everything has to be assembled manually.

---

Suppose your banking system contains:

```
Account Service

↓

Transaction Service

↓

Customer Service

↓

Loan Service

↓

Notification Service

↓

Database
```

Every one of these services depends on several other objects.

Without Spring,

you are responsible for creating every single object yourself.

Example:

```java
Database database = new Database();

NotificationService notificationService =
        new NotificationService(database);

LoanService loanService =
        new LoanService(notificationService);

CustomerService customerService =
        new CustomerService(loanService);

TransactionService transactionService =
        new TransactionService(customerService);

AccountService accountService =
        new AccountService(transactionService);
```

Imagine doing this for **800 classes.**

---

# Why Was This Bad?

Notice something.

The business logic hasn't started yet.

You're spending most of your time doing infrastructure work.

Developers should think about

```
Transfer Money

↓

Withdraw Cash

↓

Generate Statement
```

Instead they were writing

```
new

new

new

new

new

new

new
```

over and over.

---

# 2. Rise of Enterprise Java

Java became extremely popular because it solved many existing problems.

Advantages included:

- Platform independent
- Object-oriented
- Garbage Collection
- Security
- Large Standard Library
- Multi-threading

Naturally,

large companies started building enterprise software using Java.

Examples:

- Banking
- Insurance
- Telecom
- Airlines
- Government
- Healthcare

But enterprise applications have requirements beyond simple programs.

For example,

an online banking system requires:

- Authentication
- Transactions
- Logging
- Security
- Connection Pooling
- Scalability
- Messaging
- Distributed Systems

The Java language alone didn't provide these enterprise features.

So Java EE was introduced.

---

# Java EE Architecture

A simplified Java EE application looked like this.

```
Client

↓

Servlet

↓

EJB

↓

DAO

↓

Database
```

Notice the EJB layer.

EJB stood for

> Enterprise JavaBeans

It was designed to provide enterprise capabilities like

- Transactions
- Security
- Object Lifecycle
- Remote Calls

Unfortunately,

using EJB became very complicated.

---

# The Biggest Problem

Imagine writing a simple calculator.

Business requirement:

```
Add Two Numbers
```

Actual code required:

- XML

- Deployment Descriptor

- EJB Interfaces

- Container

- Application Server

- Remote Interfaces

The infrastructure code became much larger than the business logic itself.

---

# Real World Analogy

Imagine buying a microwave.

You want to heat food.

Instead,

the manufacturer says

Before using it,

please

- Build the kitchen
- Install electricity
- Assemble the microwave
- Write firmware
- Configure wiring
- Register the appliance

Only then can you heat your food.

This was how many developers felt while working with Enterprise Java.

Spring changed that.

---

# Problem 1 — Manual Dependency Creation

Suppose

OrderService needs

```
PaymentService

InventoryService

NotificationService
```

Without Spring,

the constructor might look like

```java
public OrderService() {

    PaymentService paymentService =
            new PaymentService();

    InventoryService inventoryService =
            new InventoryService();

    NotificationService notificationService =
            new NotificationService();

}
```

Now suppose

PaymentService also needs

```
Database

Logger

Configuration

Security
```

The dependency tree keeps growing.

Soon,

object creation becomes harder than business logic.

---

# Dependency Tree Explosion

```
OrderService

│

├── PaymentService

│      ├── Database

│      ├── Configuration

│      └── Logger

│

├── InventoryService

│      ├── Database

│      └── Cache

│

└── NotificationService

       ├── SMTP

       ├── SMS

       └── Kafka
```

Imagine creating this manually.

Now imagine doing this for **1000 beans.**

---

# Interview Question

## Why was manual object creation considered a problem?

Expected answer:

Manual object creation increases coupling, duplicates object creation logic, makes applications difficult to maintain, complicates testing, and causes dependency graphs to become increasingly complex as the application grows.

---

# Common Misconception

Many beginners think

> Dependency Injection exists to reduce typing.

This is incorrect.

It exists because manual dependency management **does not scale** in large enterprise applications.

Reducing code is only a side effect.

The real objective is:

- Loose coupling
- Better maintainability
- Easier testing
- Centralized object lifecycle management

---

# Summary

In this first part, we covered:

- The state of enterprise Java before Spring
- Why developers manually created every object
- How dependency graphs grew rapidly
- Why infrastructure code dominated business logic
- Why Java EE introduced EJB
- The motivation for a framework like Spring

---

## Next Part

We'll go even deeper into:

- EJB Architecture
- Remote Interfaces
- Home Interfaces
- Deployment Descriptors
- XML Hell
- Tight Coupling
- Vendor Lock-in
- Why POJOs changed everything
- How Spring challenged the EJB model

This is where you'll truly appreciate why Spring became one of the most influential Java frameworks ever created.