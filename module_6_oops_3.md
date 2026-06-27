# Module 6 - OOP (Part 3)

# Abstraction, Interfaces & Abstract Classes (`06-OOP-Part3-Abstraction-Interfaces.md`)

> **Goal:** Understand abstraction, interfaces, abstract classes, multiple inheritance using interfaces, default methods, static methods, and when to use interfaces vs abstract classes.

---

# Table of Contents

1. What is Abstraction?
2. Why Abstraction?
3. Abstract Classes
4. Abstract Methods
5. Interfaces
6. Multiple Inheritance
7. Default Methods
8. Static Methods
9. Functional Interfaces
10. Interface vs Abstract Class
11. Interview Questions
12. Best Practices
13. Exercises
14. Revision Sheet

---

# 1. What is Abstraction?

**Abstraction** means hiding implementation details and exposing only the essential functionality.

### Real-world Example

You drive a car using:

* Steering wheel
* Brake
* Accelerator

You don't need to know how the engine works internally.

Similarly, in Java:

* **What to do** → Exposed
* **How it's done** → Hidden

---

# Why Abstraction?

Benefits:

* Hides complexity
* Reduces coupling
* Improves maintainability
* Easier testing
* Encourages modular design

---

# 2. Abstract Class

An abstract class **cannot be instantiated**.

Syntax

```java
abstract class Animal{

}
```

Invalid

```java
Animal a = new Animal();
```

---

# Abstract Method

A method without implementation.

```java
abstract class Animal{

    abstract void sound();

}
```

Child class **must** implement it.

```java
class Dog extends Animal{

    @Override
    void sound(){

        System.out.println("Bark");

    }

}
```

---

# Concrete Method

Abstract classes can also contain normal methods.

```java
abstract class Animal{

    abstract void sound();

    void eat(){

        System.out.println("Eating");

    }

}
```

---

# Constructors in Abstract Classes

Yes, abstract classes **can have constructors**.

```java
abstract class Animal{

    Animal(){

        System.out.println("Animal Constructor");

    }

}
```

Constructor executes when child object is created.

---

# Fields in Abstract Classes

Allowed.

```java
abstract class Vehicle{

    String company;

}
```

---

# 3. Interface

An interface defines a **contract**.

Syntax

```java
interface Animal{

    void sound();

}
```

Implementation

```java
class Dog implements Animal{

    @Override
    public void sound(){

        System.out.println("Bark");

    }

}
```

---

# Why Interfaces?

Interfaces allow unrelated classes to share the same behavior.

Example

```text
Payment

UPI

Credit Card

Net Banking
```

All implement

```text
Payment
```

---

# Multiple Inheritance

Java does not allow

```java
class C extends A,B
```

Instead

```java
interface Flyable{

    void fly();

}

interface Swimmable{

    void swim();

}

class Duck implements Flyable,Swimmable{

}
```

One class

↓

Multiple interfaces

---

# Interface Variables

All variables are

```java
public static final
```

Example

```java
interface App{

    int PORT = 8080;

}
```

Equivalent to

```java
public static final int PORT = 8080;
```

---

# Interface Methods

Before Java 8

All methods were

```java
public abstract
```

Example

```java
void start();
```

Compiler internally treats it as

```java
public abstract void start();
```

---

# Default Methods (Java 8)

Interfaces can now contain implementation.

```java
interface Animal{

    default void sleep(){

        System.out.println("Sleeping");

    }

}
```

Purpose

* Backward compatibility
* Extend interfaces without breaking existing implementations

---

# Static Methods (Java 8)

```java
interface Utility{

    static void print(){

        System.out.println("Hello");

    }

}
```

Usage

```java
Utility.print();
```

---

# Private Methods (Java 9)

Interfaces can also contain

```java
private void helper()
```

Used internally by default methods.

---

# Functional Interface

Contains exactly **one abstract method**.

```java
@FunctionalInterface
interface Calculator{

    int add(int a,int b);

}
```

Used heavily with

* Lambda Expressions
* Streams API

We'll revisit this in Java 8.

---

# Interface Inheritance

Interfaces can extend other interfaces.

```java
interface A{

}

interface B extends A{

}
```

---

# Interface vs Abstract Class

| Interface             | Abstract Class             |
| --------------------- | -------------------------- |
| Contract              | Partial implementation     |
| Multiple inheritance  | Single inheritance         |
| No constructors       | Constructors allowed       |
| No instance variables | Instance variables allowed |
| Implements            | Extends                    |

---

# When to Use Interface?

Use when multiple classes share common behavior.

Examples

```text
Comparable

Runnable

Callable

Serializable
```

---

# When to Use Abstract Class?

Use when classes share

* Common state
* Common implementation
* Common fields

Example

```text
Employee

Manager

Developer
```

All have

* id
* name
* salary

---

# Can We Create Object?

Interface

```java
Animal a = new Animal();
```

❌ Not allowed.

Abstract Class

```java
Animal a = new Animal();
```

❌ Not allowed.

---

# Anonymous Class

```java
Animal animal = new Animal(){

    @Override
    void sound(){

        System.out.println("Bark");

    }

};
```

Useful before Java 8 Lambdas.

---

# Production Example

Spring Boot

```java
public interface UserService{

    User save(User user);

}
```

Implementation

```java
@Service
public class UserServiceImpl implements UserService{

}
```

Controller depends on

```java
UserService
```

instead of

```java
UserServiceImpl
```

This enables loose coupling and easier testing.

---

# Common Mistakes

❌ Using inheritance when an interface is sufficient.

❌ Creating large interfaces with unrelated methods.

❌ Forgetting that interface methods are public.

❌ Attempting to instantiate an abstract class or interface.

---

# Interview Questions

### What is abstraction?

---

### Difference between abstraction and encapsulation?

**Abstraction**

Hides implementation.

**Encapsulation**

Hides data.

---

### Difference between interface and abstract class?

---

### Why does Java support multiple interfaces but not multiple class inheritance?

To avoid the **Diamond Problem**.

---

### Can interfaces have constructors?

No.

---

### Can abstract classes have constructors?

Yes.

---

### Can interfaces contain variables?

Yes.

They are always

```java
public static final
```

---

### Can interfaces contain implemented methods?

Yes.

Using

* default methods
* static methods
* private methods (Java 9+)

---

### Can one interface extend another?

Yes.

---

### Can a class implement multiple interfaces?

Yes.

---

### Why are interfaces heavily used in Spring Boot?

Because they promote loose coupling, dependency injection, testing, and interchangeable implementations.

---

# Best Practices

* Program to interfaces, not implementations.
* Keep interfaces small (Interface Segregation Principle).
* Use abstract classes only when sharing state or common implementation.
* Prefer interfaces for service contracts in Spring Boot.
* Use `@FunctionalInterface` for single-method interfaces.

---

# Exercises

1. Create an abstract class `Vehicle` with an abstract method `start()`.
2. Create `Car` and `Bike` classes implementing `start()`.
3. Create an interface `Payment` with a `pay()` method.
4. Implement `UPIPayment` and `CreditCardPayment`.
5. Add a default method to the interface.
6. Create a functional interface for mathematical operations.

---

# Revision Sheet

* Abstraction
* Abstract Class
* Abstract Method
* Concrete Method
* Interface
* implements
* Multiple Inheritance
* Default Methods
* Static Methods
* Private Methods
* Functional Interface
* Interface vs Abstract Class
* Anonymous Class
* Spring Boot Usage
* Interview Questions
