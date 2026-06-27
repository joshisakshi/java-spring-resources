# Module 10 - Generics (Part 2)

# Bounded Generics

> **Goal:** Learn how to restrict generic types using bounds, understand upper bounds, multiple bounds, generic methods with bounds, and where bounded generics are used in Java and Spring Boot.

---

# Table of Contents

1. Why Bounded Generics?
2. Upper Bounded Type Parameters
3. Generic Methods with Bounds
4. Multiple Bounds
5. Comparable with Generics
6. Internal Working
7. Spring Boot Usage
8. Best Practices
9. Common Mistakes
10. Interview Questions
11. Exercises
12. Revision Sheet

---

# 1. Why Bounded Generics?

So far we wrote:

```java
class Box<T> {

    private T value;

}
```

This means **T can be anything**.

Examples:

```java
Box<String>

Box<Integer>

Box<Employee>

Box<Car>
```

Sometimes this flexibility is **too much**.

Suppose we want a method that calculates the square of a number.

```java
public static <T> double square(T value) {

    // Not possible

}
```

The compiler has no guarantee that `T` is a number.

---

# Solution

Restrict `T` to numeric types.

```java
<T extends Number>
```

Now `T` can only be:

```text
Integer

Double

Float

Long

Short

Byte
```

---

# 2. Upper Bounded Type Parameters

Syntax:

```java
<T extends Number>
```

Example:

```java
class Calculator<T extends Number> {

    private T value;

    public Calculator(T value) {

        this.value = value;

    }

    public double square() {

        return value.doubleValue() * value.doubleValue();

    }

}
```

Usage:

```java
Calculator<Integer> c1 =
        new Calculator<>(10);

Calculator<Double> c2 =
        new Calculator<>(12.5);
```

---

# Invalid Example

```java
Calculator<String> c =
        new Calculator<>("Java");
```

Compile-time error.

Reason:

```text
String

❌

is not a subclass of Number
```

---

# Available Number Methods

Since `T extends Number`,

all Number methods become available.

```java
value.intValue()

value.doubleValue()

value.longValue()

value.floatValue()
```

---

# 3. Generic Methods with Bounds

Bounds can also be applied to methods.

```java
public static <T extends Number>
double cube(T value){

    return Math.pow(
        value.doubleValue(),
        3
    );

}
```

Usage:

```java
cube(5);

cube(10.5);
```

---

# 4. Multiple Bounds

A generic type can extend **one class** and implement **multiple interfaces**.

Syntax:

```java
<T extends ClassName
    & Interface1
    & Interface2>
```

Example:

```java
class Demo<
    T extends Number
      & Comparable<T>
>{

}
```

Rules:

- First bound must be a class (if present).
- Remaining bounds must be interfaces.

---

# Invalid Example

```java
<T extends Comparable<T>
    & Number>
```

Compile-time error.

Reason:

The class must appear before interfaces.

---

# 5. Comparable with Generics

Suppose we want to compare two values.

```java
public static <T extends Comparable<T>>
T max(T a, T b){

    if(a.compareTo(b) > 0){

        return a;

    }

    return b;

}
```

Usage:

```java
max(10,20);

max("Java","Spring");
```

Works because:

```text
Integer

implements Comparable<Integer>

String

implements Comparable<String>
```

---

# Why Comparable?

Without it,

the compiler wouldn't know whether `T` has:

```java
compareTo()
```

Adding the bound guarantees the method exists.

---

# 6. Internal Working

At compile time:

```java
<T extends Number>
```

allows methods like:

```java
doubleValue()
```

After compilation,

Java performs **Type Erasure**.

Conceptually:

```text
<T extends Number>

↓

Number
```

So internally the compiler treats `T` as its bound.

We'll study Type Erasure in detail in Part 4.

---

# 7. Spring Boot Usage

Bounded generics appear frequently in framework code.

Example:

```java
class BaseRepository<
    T extends BaseEntity
>{

}
```

Only entities extending `BaseEntity` are allowed.

Another example:

```java
class BaseService<
    T extends BaseModel
>{

}
```

This enables reusable service layers while ensuring only valid domain types are used.

---

# 8. Best Practices

✅ Use bounds when your logic depends on specific methods.

Example:

```java
<T extends Number>
```

instead of:

```java
<T>
```

---

✅ Keep bounds as general as possible.

If `Number` is sufficient,

don't restrict to `Integer`.

---

✅ Use `Comparable<T>` when natural ordering is required.

---

# 9. Common Mistakes

❌ Forgetting that:

```java
extends
```

is used for both:

- Classes
- Interfaces

Example:

```java
<T extends Comparable<T>>
```

Even though `Comparable` is an interface.

---

❌ Incorrect order in multiple bounds.

Wrong:

```java
<T extends Comparable<T>
    & Number>
```

Correct:

```java
<T extends Number
    & Comparable<T>>
```

---

❌ Assuming bounded generics affect runtime.

They are enforced at compile time and erased later.

---

# 10. Interview Questions

### What are bounded generics?

---

### Why use `<T extends Number>`?

---

### Difference between:

```java
<T>
```

and

```java
<T extends Number>
```

---

### Why is `extends` used for interfaces too?

---

### Can a generic type have multiple bounds?

---

### What is the rule for multiple bounds?

---

### Why use:

```java
<T extends Comparable<T>>
```

---

### What happens after compilation?

---

### Where are bounded generics used in Spring Boot?

---

# 11. Exercises

1. Create a generic `Calculator<T extends Number>`.
2. Write a generic `max()` method using `Comparable`.
3. Create a class with multiple bounds.
4. Try passing a `String` to `Calculator<T extends Number>` and observe the compiler error.
5. Find examples of bounded generics in the JDK or Spring APIs.

---

# 12. Revision Sheet

## Core Concepts

- Bounded Generics
- Upper Bound
- Multiple Bounds

## Syntax

```java
<T extends Number>

<T extends Comparable<T>>

<T extends Number & Comparable<T>>
```

## Common Methods

```java
doubleValue()

intValue()

compareTo()
```

## Spring Boot

- BaseRepository<T extends BaseEntity>
- BaseService<T extends BaseModel>

## Interview Focus

- Why bounds are needed
- Multiple bounds
- Comparable with Generics
- Compile-time restrictions
- Type Erasure introduction