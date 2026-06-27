# Module 6 - OOP (Part 4)

# Relationships, SOLID Principles & Object-Oriented Design (`06-OOP-Part4-Relationships-SOLID.md`)

> **Goal:** Master object relationships (Association, Aggregation, Composition, Dependency), Coupling & Cohesion, SOLID principles, and understand how these concepts are applied in Spring Boot applications.

---

# Table of Contents

1. Object Relationships
2. Association
3. Aggregation
4. Composition
5. Dependency
6. IS-A vs HAS-A
7. Coupling
8. Cohesion
9. SOLID Principles
10. Spring Boot Examples
11. Interview Questions
12. Best Practices
13. Exercises
14. Revision Sheet

---

# 1. Object Relationships

Objects rarely work alone.

A typical backend application consists of objects interacting with each other.

Example

```text id="1m6t5n"
User

↓

Order

↓

Payment

↓

Invoice
```

Understanding these relationships helps in designing maintainable systems.

---

# 2. Association

Association means **two independent objects know about each other**.

Example

```text id="zq4hnk"
Teacher
      ↔
Student
```

A teacher can exist without a student.

A student can exist without a teacher.

Example

```java id="x7o8n3"
class Teacher{

}

class Student{

    Teacher teacher;

}
```

Ownership?

**No**

Lifecycle?

Independent.

---

# 3. Aggregation

Aggregation is a **HAS-A** relationship with **weak ownership**.

Example

```text id="n1dtj5"
Department

HAS-A

Professor
```

If the department is removed,

the professor can still exist.

Example

```java id="m4w7ha"
class Professor{

}

class Department{

    private Professor professor;

}
```

Ownership?

Weak.

Lifecycle?

Independent.

---

# 4. Composition

Composition is also a **HAS-A** relationship but with **strong ownership**.

Example

```text id="h2p6r4"
House

HAS-A

Room
```

If the house is destroyed,

its rooms are also destroyed.

Example

```java id="j6r2xa"
class Room{

}

class House{

    private Room room = new Room();

}
```

Ownership?

Strong.

Lifecycle?

Dependent.

---

# Aggregation vs Composition

| Aggregation           | Composition             |
| --------------------- | ----------------------- |
| Weak ownership        | Strong ownership        |
| Independent lifecycle | Dependent lifecycle     |
| Object shared         | Object owned            |
| Can exist separately  | Cannot exist separately |

---

# 5. Dependency

Dependency means one class **temporarily uses another**.

Example

```java id="z5v8tb"
class EmailService{

    void send(){

    }

}

class Notification{

    void notifyUser(EmailService service){

        service.send();

    }

}
```

The object is **used**, not owned.

---

# 6. IS-A vs HAS-A

## IS-A

Inheritance

```text id="h5v0pc"
Dog IS-A Animal
```

Implemented using

```java id="oe3zgp"
extends
```

---

## HAS-A

Composition / Aggregation

```text id="u4grs8"
Car HAS-A Engine
```

Implemented using

```java id="m8dyv5"
private Engine engine;
```

---

# Which One Should You Prefer?

Modern software design recommends:

> **Favor Composition over Inheritance**

Reason

Composition is:

* More flexible
* Easier to extend
* Less tightly coupled

---

# 7. Coupling

Coupling measures **how dependent one class is on another**.

## Tight Coupling

```java id="j3e1fw"
class UserService{

    private MySQLDatabase db = new MySQLDatabase();

}
```

Problems

* Hard to test
* Hard to replace
* Hard to maintain

---

## Loose Coupling

```java id="i4ht1f"
interface Database{

    void save();

}

class UserService{

    private Database database;

}
```

Spring injects the implementation.

Much more flexible.

---

# 8. Cohesion

Cohesion measures **how focused a class is on a single responsibility**.

High Cohesion

```java id="4vqgdu"
UserService

↓

Only user-related operations
```

Low Cohesion

```java id="u5pw9g"
UserService

↓

Save User

Send Email

Generate Invoice

Upload File

Print Report
```

Violates the Single Responsibility Principle.

---

# 9. SOLID Principles

SOLID is a set of five design principles for writing maintainable object-oriented software.

---

# S — Single Responsibility Principle (SRP)

A class should have **only one reason to change**.

❌ Bad

```java id="oew1zb"
class UserService{

    saveUser();

    sendEmail();

    generateInvoice();

}
```

✅ Good

```java id="5hj3dz"
UserService

EmailService

InvoiceService
```

Each class has one responsibility.

---

# O — Open Closed Principle (OCP)

Software entities should be

* Open for extension
* Closed for modification

❌ Bad

```java id="mpc7cm"
if(paymentType.equals("UPI")){

}
else if(paymentType.equals("CARD")){

}
```

✅ Good

```java id="wjlwm8"
interface Payment{

    void pay();

}
```

New payment methods only require a new implementation.

No existing code changes.

---

# L — Liskov Substitution Principle (LSP)

Child classes should be usable wherever parent classes are expected.

Good

```java id="8lxqrs"
Animal

↓

Dog
```

Bad example

```text id="7pzjlwm"
Bird

↓

Penguin
```

If `Bird.fly()` exists,

Penguin cannot correctly substitute it.

Design is flawed.

---

# I — Interface Segregation Principle (ISP)

Clients should not depend on methods they don't use.

❌ Bad

```java id="t2w5fz"
interface Machine{

    print();

    scan();

    fax();

}
```

A simple printer is forced to implement unnecessary methods.

✅ Good

```java id="7qub4o"
Printer

Scanner

FaxMachine
```

Separate interfaces.

---

# D — Dependency Inversion Principle (DIP)

Depend on abstractions,

not concrete implementations.

❌ Bad

```java id="xk2w8r"
UserService

↓

MySQLDatabase
```

✅ Good

```java id="5h3jlwm"
UserService

↓

Database Interface

↓

MySQLDatabase

↓

MongoDatabase
```

Spring Boot's Dependency Injection is built around this principle.

---

# Spring Boot Connection

Example

```java id="i8v31h"
public interface UserRepository{

}
```

Implementation

```java id="c4u8xq"
@Repository
public class UserRepositoryImpl implements UserRepository{

}
```

Service

```java id="1g3jlwm"
@Service
public class UserService{

    private final UserRepository repository;

}
```

The service depends on the interface, not the implementation.

This demonstrates:

* Loose Coupling
* Dependency Inversion
* High Cohesion

---

# Real Project Example

```text id="9wjlwm"
Controller

↓

Service

↓

Repository

↓

Database
```

Each layer has a single responsibility.

This architecture follows several SOLID principles naturally.

---

# Common Mistakes

❌ Using inheritance where composition is better.

❌ Creating "God Classes" with many unrelated responsibilities.

❌ Depending directly on concrete classes.

❌ Designing large interfaces with unrelated methods.

❌ Confusing Aggregation with Composition.

---

# Interview Questions

### What is Association?

---

### Difference between Aggregation and Composition?

---

### Difference between IS-A and HAS-A?

---

### Why is Composition preferred over Inheritance?

---

### What is Tight Coupling?

---

### What is Loose Coupling?

---

### What is High Cohesion?

---

### Explain all five SOLID principles.

---

### Which SOLID principle does Spring Dependency Injection implement?

**Dependency Inversion Principle (DIP)**

---

### Which SOLID principle is most commonly violated by beginners?

**Single Responsibility Principle (SRP)**

---

# Best Practices

* Prefer Composition over Inheritance.
* Design small, focused classes.
* Program to interfaces, not implementations.
* Keep coupling low and cohesion high.
* Apply SOLID principles consistently.

---

# Exercises

1. Model a `University` and `Student` using Aggregation.
2. Model a `House` and `Room` using Composition.
3. Design a payment system following the Open-Closed Principle.
4. Refactor a tightly coupled service using Dependency Injection.
5. Identify which SOLID principle is violated in a given code snippet.

---

# Revision Sheet

* Association
* Aggregation
* Composition
* Dependency
* IS-A
* HAS-A
* Coupling
* Cohesion
* SOLID Principles
* SRP
* OCP
* LSP
* ISP
* DIP
* Spring Dependency Injection
* Interview Questions
