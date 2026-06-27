# Module 10 - Generics (Part 1)

# Introduction to Generics

> Goal: Understand why Generics exist, how they provide type safety, how they work internally, and where they are used in Java and Spring Boot.

---

# Table of Contents

1. What are Generics?
2. Why were Generics introduced?
3. Problems Before Generics
4. Type Safety
5. Generic Classes
6. Generic Interfaces
7. Generic Methods
8. Type Inference
9. Diamond Operator
10. Internal Working
11. Spring Boot Usage
12. Best Practices
13. Common Mistakes
14. Interview Questions
15. Exercises
16. Revision Sheet

---

# 1. What are Generics?

Generics allow classes, interfaces and methods to work with **different data types while maintaining compile-time type safety**.

Instead of writing separate classes for Integer, String, Employee, etc., we write one reusable implementation.

Example:

```java
List<String> names = new ArrayList<>();

List<Integer> numbers = new ArrayList<>();
```

Same class.

Different data types.

---

# Why are Generics Needed?

Without Generics:

```java
List list = new ArrayList();

list.add("Java");

list.add(100);
```

Everything is stored as:

```text
Object
```

When retrieving:

```java
String s = (String) list.get(0);
```

Requires casting.

Dangerous.

---

# Runtime Problem

```java
List list = new ArrayList();

list.add(10);

String s = (String) list.get(0);
```

Output:

```text
ClassCastException
```

---

# With Generics

```java
List<String> list =
        new ArrayList<>();

list.add("Java");

// list.add(100); ❌ Compile-time error
```

Compiler prevents invalid types.

---

# Advantages

✅ Compile-time type checking

✅ No explicit casting

✅ Code reuse

✅ Better readability

✅ Fewer runtime errors

---

# 2. Generic Class

Syntax

```java
class Box<T>{

    private T value;

    public void set(T value){

        this.value = value;

    }

    public T get(){

        return value;

    }

}
```

Usage

```java
Box<String> box = new Box<>();

box.set("Spring");

System.out.println(box.get());
```

Output

```text
Spring
```

---

# Another Example

```java
Box<Integer> box =
        new Box<>();

box.set(100);
```

Same class.

Different type.

---

# What Does T Mean?

`T` is just a placeholder.

Common conventions:

| Symbol | Meaning |
|---------|----------|
| T | Type |
| E | Element |
| K | Key |
| V | Value |
| N | Number |

These names are conventions, not keywords.

---

# Multiple Type Parameters

```java
class Pair<K,V>{

    private K key;

    private V value;

}
```

Example

```java
Pair<Integer,String> student =
        new Pair<>();
```

---

# 3. Generic Interface

```java
interface Repository<T>{

    void save(T obj);

}
```

Implementation

```java
class UserRepository
implements Repository<User>{

    @Override
    public void save(User user){

    }

}
```

Spring Data repositories are built on this idea.

---

# 4. Generic Method

Methods can have their own type parameter.

```java
public static <T> void print(T value){

    System.out.println(value);

}
```

Usage

```java
print("Java");

print(100);

print(12.5);
```

One method.

Any type.

---

# Generic Return Type

```java
public static <T> T first(T a,T b){

    return a;

}
```

Compiler infers the type.

---

# 5. Type Inference

Java often determines the generic type automatically.

Example

```java
Box<String> box =
        new Box<>();
```

Notice:

```java
new Box<>();
```

No need to repeat:

```java
new Box<String>();
```

Compiler infers the type.

---

# 6. Diamond Operator

Introduced in Java 7.

Old

```java
Map<Integer,String> map =
    new HashMap<Integer,String>();
```

Modern

```java
Map<Integer,String> map =
    new HashMap<>();
```

Cleaner.

Less repetition.

---

# 7. Internal Working

This is a very common interview question.

Does Java create separate classes?

```text
Box<String>

Box<Integer>

Box<Employee>
```

Answer:

**No.**

Java uses **Type Erasure**.

At runtime:

```text
Box<T>

↓

Box<Object>
```

Generic type information is removed after compilation.

We'll study this in depth in Part 4.

---

# Compile-Time vs Runtime

Compile Time

```java
List<String>
```

Runtime

```text
ArrayList
```

The JVM does not know that it was `List<String>`.

---

# 8. Spring Boot Usage

Generics are everywhere.

```java
JpaRepository<User, Long>
```

```java
ResponseEntity<User>
```

```java
Optional<User>
```

```java
List<Employee>
```

```java
Map<String,Object>
```

```java
Page<User>
```

Understanding Generics is essential for Spring Boot.

---

# 9. Best Practices

✅ Prefer interfaces.

```java
List<String> list =
        new ArrayList<>();
```

---

✅ Use Generics instead of raw types.

---

✅ Avoid unnecessary casting.

---

✅ Use meaningful generic names.

---

# 10. Common Mistakes

❌ Using raw collections.

```java
List list =
        new ArrayList();
```

---

❌ Ignoring compiler warnings.

---

❌ Thinking Generics exist at runtime.

---

❌ Using Object instead of Generics.

---

# 11. Interview Questions

### What are Generics?

---

### Why were Generics introduced?

---

### Advantages of Generics?

---

### Difference between raw types and generic types?

---

### What is a Generic Class?

---

### What is a Generic Method?

---

### What is the Diamond Operator?

---

### What is Type Safety?

---

### Why does Generics reduce ClassCastException?

---

### Where are Generics used in Spring Boot?

---

# 12. Exercises

1. Create a generic Box class.
2. Create a Pair<K,V> class.
3. Write a generic print() method.
4. Compare raw types and generic types.
5. Find five generic classes in Spring Boot.

---

# 13. Revision Sheet

## Core Concepts

- Generic Class
- Generic Interface
- Generic Method

## Symbols

- T
- E
- K
- V

## Java Features

- Diamond Operator
- Type Inference

## Spring Boot

- JpaRepository<T,ID>
- ResponseEntity<T>
- Optional<T>

## Interview Focus

- Type Safety
- Raw Types
- Compile Time
- Runtime
- Type Erasure (Introduction)