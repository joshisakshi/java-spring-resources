# Module 6 - OOP (Part 2)

# Inheritance, Polymorphism, Method Overriding & `super` (`06-OOP-Part2-Inheritance-Polymorphism.md`)

> **Goal:** Understand inheritance, method overriding, runtime polymorphism, dynamic method dispatch, the `super` keyword, `final`, and the Object class.

---

# Table of Contents

1. Inheritance
2. Types of Inheritance
3. `extends`
4. IS-A Relationship
5. Method Overriding
6. Runtime Polymorphism
7. Dynamic Method Dispatch
8. `super` Keyword
9. `final`
10. Object Class
11. Interview Questions
12. Best Practices
13. Exercises
14. Revision Sheet

---

# 1. Inheritance

Inheritance allows one class to acquire the properties and behaviors of another class.

Purpose:

* Code Reuse
* Extensibility
* Maintainability

Example

```java
class Animal{

    void eat(){
        System.out.println("Eating");
    }

}

class Dog extends Animal{

    void bark(){
        System.out.println("Barking");
    }

}
```

Usage

```java
Dog dog = new Dog();

dog.eat();

dog.bark();
```

---

# IS-A Relationship

Inheritance represents an **IS-A** relationship.

```text
Dog IS-A Animal

Car IS-A Vehicle

SavingsAccount IS-A BankAccount
```

If the relationship is not IS-A, inheritance is probably the wrong choice.

---

# Types of Inheritance

Java supports

```text
Single

Multilevel

Hierarchical
```

Example

```text
Animal
   │
 Mammal
   │
  Dog
```

Java **does not support multiple inheritance with classes**.

```java
class A{}

class B{}

class C extends A,B{}   // Invalid
```

Reason:

Diamond Problem.

---

# Why Interfaces?

Multiple inheritance is achieved using interfaces.

```java
interface Flyable{}

interface Swimmable{}

class Duck implements Flyable, Swimmable{

}
```

---

# `extends`

Syntax

```java
class Child extends Parent{

}
```

The child automatically inherits

* Fields
* Methods

Except

* Private members
* Constructors

---

# Constructor Execution Order

```java
class Animal{

    Animal(){

        System.out.println("Animal");

    }

}

class Dog extends Animal{

    Dog(){

        System.out.println("Dog");

    }

}
```

Output

```text
Animal
Dog
```

Parent constructor executes first.

---

# 2. Method Overriding

Child provides its own implementation.

Parent

```java
class Animal{

    void sound(){

        System.out.println("Animal");

    }

}
```

Child

```java
class Dog extends Animal{

    @Override
    void sound(){

        System.out.println("Bark");

    }

}
```

---

# Rules

Same

* Method Name
* Parameters
* Return Type (or covariant return type)

Cannot reduce visibility.

Wrong

```java
public void test()
```

↓

```java
private void test()
```

---

# `@Override`

Always use

```java
@Override
```

Benefits

* Compiler checks mistakes.
* Improves readability.

---

# Compile-Time vs Runtime Polymorphism

Compile Time

```java
add(int,int)

add(double,double)
```

Method Overloading

Runtime

```java
Animal a = new Dog();

a.sound();
```

Method Overriding

---

# Runtime Polymorphism

Example

```java
Animal animal = new Dog();

animal.sound();
```

Output

```text
Bark
```

Reference type

↓

Animal

Object type

↓

Dog

Actual object's method executes.

---

# Dynamic Method Dispatch

Decision is made **at runtime**.

Memory

```text
Animal animal
      │
      ▼
    Dog Object
```

Compiler checks

```text
Animal
```

JVM executes

```text
Dog
```

This is Dynamic Method Dispatch.

---

# Upcasting

Automatically allowed.

```java
Dog dog = new Dog();

Animal animal = dog;
```

Safe.

---

# Downcasting

```java
Animal animal = new Dog();

Dog dog = (Dog) animal;
```

Allowed only if object is actually a Dog.

Wrong

```java
Animal animal = new Animal();

Dog dog = (Dog) animal;
```

Runtime

```text
ClassCastException
```

---

# `instanceof`

Check object type before casting.

```java
if(animal instanceof Dog){

    Dog dog = (Dog) animal;

}
```

---

# The `super` Keyword

Used to refer to parent class.

---

## Parent Variable

```java
class Animal{

    String name = "Animal";

}

class Dog extends Animal{

    String name = "Dog";

    void print(){

        System.out.println(super.name);

    }

}
```

Output

```text
Animal
```

---

## Parent Method

```java
super.sound();
```

Calls parent implementation.

---

## Parent Constructor

```java
class Dog extends Animal{

    Dog(){

        super();

    }

}
```

`super()` is automatically inserted by the compiler if not written explicitly.

---

# Method Hiding

Static methods are **hidden**, not overridden.

```java
class A{

    static void print(){}

}

class B extends A{

    static void print(){}

}
```

---

# The `final` Keyword

## Final Variable

Cannot change.

```java
final int age = 20;
```

---

## Final Method

Cannot be overridden.

```java
final void display(){}
```

---

## Final Class

Cannot be inherited.

```java
final class Utility{}
```

Example

```text
String
```

is final.

---

# Object Class

Every Java class extends

```text
Object
```

Even if you don't write it.

```java
class Employee{

}
```

Actually becomes

```java
class Employee extends Object{

}
```

---

# Important Object Methods

```java
toString()

equals()

hashCode()

clone()

getClass()

wait()

notify()

notifyAll()
```

These will be revisited in Collections.

---

# Overriding `toString()`

Default

```java
System.out.println(emp);
```

Output

```text
Employee@4a54c0de
```

Override

```java
@Override
public String toString(){

    return name;

}
```

Output

```text
Sakshi
```

---

# Overriding `equals()`

Default

Compares references.

```java
emp1 == emp2
```

After overriding

Can compare object contents.

---

# Overriding `hashCode()`

Whenever `equals()` is overridden,

`hashCode()` should also be overridden.

Very important for

* HashMap
* HashSet
* LinkedHashMap

---

# Common Mistakes

❌ Using inheritance instead of composition.

❌ Forgetting `@Override`.

❌ Downcasting without checking `instanceof`.

❌ Overriding static methods.

❌ Forgetting to override `hashCode()` with `equals()`.

---

# Production Example

```java
class Employee{

}

class Manager extends Employee{

}

class Developer extends Employee{

}
```

```java
List<Employee> employees = List.of(

    new Manager(),

    new Developer()

);
```

Loop

```java
for(Employee e : employees){

    e.work();

}
```

Each object executes its own implementation.

This is runtime polymorphism and is heavily used in Spring Boot.

---

# Interview Questions

### What is inheritance?

---

### Difference between overloading and overriding?

---

### Explain runtime polymorphism.

---

### What is dynamic method dispatch?

---

### Why doesn't Java support multiple inheritance?

---

### Difference between `this` and `super`?

---

### Can constructors be inherited?

**No.**

---

### Can private methods be overridden?

**No.**

---

### Can static methods be overridden?

**No. They are hidden.**

---

### Difference between IS-A and HAS-A?

IS-A → Inheritance

HAS-A → Composition (covered in Part 4)

---

### Why should `equals()` and `hashCode()` be overridden together?

Required for correct behavior of hash-based collections.

---

# Best Practices

* Prefer composition over inheritance when possible.
* Use `@Override`.
* Keep inheritance hierarchies shallow.
* Use polymorphism instead of multiple `if-else` chains.
* Override `toString()`, `equals()`, and `hashCode()` for domain objects.

---

# Exercises

1. Create `Animal`, `Dog`, and `Cat` classes.
2. Override the `sound()` method.
3. Demonstrate runtime polymorphism.
4. Demonstrate upcasting and downcasting.
5. Override `toString()`.
6. Override `equals()` and `hashCode()`.

---

# Revision Sheet

* Inheritance
* IS-A Relationship
* `extends`
* Method Overriding
* Runtime Polymorphism
* Dynamic Method Dispatch
* Upcasting
* Downcasting
* `instanceof`
* `super`
* `final`
* Object Class
* `toString()`
* `equals()`
* `hashCode()`
* Method Hiding
* Interview Questions
