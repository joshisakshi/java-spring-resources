# Module 6 - OOP (Part 1)

# Classes, Objects, Constructors & Object Lifecycle (`06-OOP-Part1-Classes-Objects-Constructors.md`)

> **Goal:** Understand the foundation of Object-Oriented Programming by learning classes, objects, constructors, object creation, memory allocation, `this` keyword, constructor chaining, and object lifecycle.

---

# Table of Contents

1. What is OOP?
2. Why OOP?
3. Class
4. Object
5. Class vs Object
6. Object Creation
7. Memory Representation
8. Constructors
9. Constructor Overloading
10. Default Constructor
11. Parameterized Constructor
12. `this` Keyword
13. Constructor Chaining
14. Object Lifecycle
15. Interview Questions
16. Best Practices
17. Exercises
18. Revision Sheet

---

# 1. What is Object-Oriented Programming (OOP)?

Object-Oriented Programming is a programming paradigm where software is built using **objects** that contain both **data (state)** and **behavior (methods)**.

Instead of thinking in terms of functions, OOP models real-world entities.

Example:

* Employee
* User
* BankAccount
* Product
* Order

Each object has:

* **State** → Variables (fields)
* **Behavior** → Methods

---

# Why OOP?

Without OOP:

* Code duplication
* Difficult maintenance
* Poor scalability

With OOP:

* Code Reusability
* Modularity
* Maintainability
* Extensibility
* Better Testing

---

# 2. Class

A **class** is a blueprint used to create objects.

Example:

```java
public class Employee {

    String name;
    int age;

    void work() {
        System.out.println("Working...");
    }
}
```

The class itself does **not** occupy heap memory for its fields. It defines what an object should look like.

---

# 3. Object

An object is a **runtime instance of a class**.

```java
Employee emp = new Employee();
```

Now an actual Employee object exists in memory.

---

# Class vs Object

| Class             | Object                  |
| ----------------- | ----------------------- |
| Blueprint         | Instance                |
| Logical entity    | Physical entity         |
| Created once      | Can have many instances |
| Defines structure | Holds actual data       |

---

# 4. Object Creation

```java
Employee emp = new Employee();
```

Let's understand what happens internally.

### Step 1

Compiler checks whether the class exists.

↓

### Step 2

`new`

allocates memory inside the Heap.

↓

### Step 3

Constructor executes.

↓

### Step 4

Reference is stored inside Stack memory.

---

# Memory Representation

```text
Stack Memory

emp
 │
 ▼

Heap Memory

-------------------
Employee Object
-------------------
name = null
age = 0
-------------------
```

`emp` is **not** the object.

It only stores the **reference (address)** of the object.

---

# Multiple Objects

```java
Employee emp1 = new Employee();
Employee emp2 = new Employee();
```

Memory

```text
Stack

emp1 ------
           \
            \
             ▼

        Employee Object 1

emp2 ------

             ▼

        Employee Object 2
```

Each object has its own copy of instance variables.

---

# 5. Constructors

A constructor initializes an object.

Syntax

```java
public Employee() {

}
```

Rules

* Same name as class
* No return type
* Executes automatically
* Called only during object creation

---

# Default Constructor

If no constructor is written,

Java automatically creates one.

Equivalent to:

```java
public Employee() {

}
```

As soon as you write **any constructor**, Java stops providing the default constructor.

---

# Parameterized Constructor

```java
public Employee(String name, int age){

    this.name = name;
    this.age = age;

}
```

Usage

```java
Employee emp = new Employee("Sakshi",24);
```

---

# Constructor Overloading

Multiple constructors with different parameter lists.

```java
Employee()

Employee(String name)

Employee(String name,int age)
```

This allows flexible object creation.

---

# 6. The `this` Keyword

`this` refers to the **current object**.

Without `this`

```java
public Employee(String name){

    name = name;

}
```

Both variables refer to the parameter.

The instance variable remains unchanged.

Correct

```java
public Employee(String name){

    this.name = name;

}
```

`this.name`

↓

Current object's field.

---

# Constructor Chaining

One constructor can call another using `this()`.

```java
public Employee(){

    this("Unknown",0);

}

public Employee(String name,int age){

    this.name=name;
    this.age=age;

}
```

Benefits

* Avoid duplicate initialization code.
* Centralize constructor logic.

---

# Object Lifecycle

```text
new Employee()

↓

Memory Allocated

↓

Constructor Executes

↓

Object Used

↓

Reference Lost

↓

Garbage Collector Removes Object
```

Objects are destroyed automatically by the Garbage Collector when no reachable references remain.

---

# `new` Keyword

Responsibilities of `new`

* Allocate Heap memory.
* Initialize instance variables with default values.
* Invoke constructor.
* Return object reference.

---

# Instance Variables vs Local Variables

```java
class Employee{

    int age;

    void display(){

        int salary=50000;

    }

}
```

| Instance Variable | Local Variable          |
| ----------------- | ----------------------- |
| Heap              | Stack                   |
| Default Value     | No Default Value        |
| Lives with Object | Lives until Method Ends |

---

# Common Mistakes

❌ Forgetting to initialize objects.

```java
Employee emp;

emp.work();
```

Result

```text
NullPointerException
```

---

❌ Confusing class with object.

```java
Employee
```

is **not** an object.

---

❌ Forgetting `this`.

```java
name=name;
```

---

# Production Example

```java
public class User {

    private Long id;
    private String name;

    public User(Long id,String name){

        this.id=id;
        this.name=name;

    }

}
```

Spring Boot creates thousands of such objects while handling HTTP requests.

---

# Interview Questions

### Q1. Difference between Class and Object?

### Q2. Why is a constructor not a method?

### Q3. Can constructors be overloaded?

### Q4. Can constructors be inherited?

**No.**

### Q5. Can constructors be private?

**Yes.**

Used in the Singleton Design Pattern.

### Q6. What does the `new` keyword do internally?

### Q7. Explain object creation step-by-step.

### Q8. Why is `this` required?

---

# Best Practices

* Keep constructors simple.
* Initialize mandatory fields through constructors.
* Use constructor overloading instead of duplicate logic.
* Use meaningful class names.
* Prefer immutability where possible.

---

# Exercises

1. Create a `Student` class with three fields.
2. Create default and parameterized constructors.
3. Print object details.
4. Demonstrate constructor overloading.
5. Demonstrate constructor chaining using `this()`.
6. Draw the Stack and Heap memory for two `Student` objects.

---

# Revision Sheet

* OOP
* Class
* Object
* Class vs Object
* `new`
* Constructors
* Default Constructor
* Parameterized Constructor
* Constructor Overloading
* `this`
* Constructor Chaining
* Stack vs Heap
* Object Lifecycle
* Object Creation Process
* Interview Questions
