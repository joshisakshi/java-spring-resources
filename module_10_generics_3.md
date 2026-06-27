# Module 10 - Generics (Part 3)

# Wildcards (? extends ? super) & PECS

> **Goal:** Master Java wildcards, understand covariance and contravariance, learn PECS (Producer Extends Consumer Super), and know when to use each wildcard in interviews and Spring Boot.

---

# Table of Contents

1. Why Wildcards?
2. Invariance in Generics
3. Unbounded Wildcards (<?>)
4. Upper Bounded Wildcards (? extends T)
5. Lower Bounded Wildcards (? super T)
6. PECS Principle
7. Covariance & Contravariance
8. Internal Working
9. Spring Boot Usage
10. Best Practices
11. Common Mistakes
12. Interview Questions
13. Exercises
14. Revision Sheet

---

# 1. Why Wildcards?

Suppose we have:

```java
List<Integer> integers =
        new ArrayList<>();
```

Now consider:

```java
List<Number> numbers = integers;
```

Will this compile?

**No.**

Compile-time error.

Even though:

```text
Integer

extends

Number
```

Generics do **not** follow inheritance in this way.

---

# Why?

Imagine Java allowed this:

```java
List<Integer> integers =
        new ArrayList<>();

List<Number> numbers = integers;

numbers.add(12.5);
```

Now the original list contains:

```text
Integer

Double
```

Impossible.

Type safety would be broken.

Therefore:

```text
List<Integer>

≠

List<Number>
```

This property is called **invariance**.

---

# 2. Invariance

Inheritance:

```text
Integer

↓

Number

↓

Object
```

Generics:

```text
List<Integer>

✗

List<Number>

✗

List<Object>
```

Each generic type is independent.

---

# 3. Unbounded Wildcard

Syntax:

```java
List<?>
```

Meaning:

"A list of some unknown type."

Example:

```java
public void print(List<?> list){

    for(Object obj : list){

        System.out.println(obj);

    }

}
```

Usage:

```java
print(List.of(1,2,3));

print(List.of("Java","Spring"));

print(List.of(10.5,20.5));
```

---

# Can We Add Elements?

```java
List<?> list;
```

Allowed:

```java
list.add(null);
```

Not allowed:

```java
list.add(10);

list.add("Java");
```

Reason:

The compiler does not know the actual type.

---

# Reading

Reading is always safe.

```java
Object obj = list.get(0);
```

Returned type:

```text
Object
```

---

# 4. Upper Bounded Wildcard

Syntax:

```java
<? extends Number>
```

Meaning:

"Some unknown type that extends Number."

Possible types:

```text
Integer

Double

Float

Long
```

Example:

```java
public static double sum(
        List<? extends Number> list){

    double total = 0;

    for(Number n : list){

        total += n.doubleValue();

    }

    return total;

}
```

Usage:

```java
sum(List.of(1,2,3));

sum(List.of(10.5,20.5));
```

---

# Can We Add?

```java
List<? extends Number>
```

Allowed:

```java
list.add(null);
```

Not allowed:

```java
list.add(10);

list.add(10.5);
```

Why?

Because Java doesn't know the exact subtype.

The actual list might be:

```java
List<Integer>
```

or

```java
List<Double>
```

Adding a `Double` to a `List<Integer>` would be unsafe.

---

# Reading

Reading is safe.

```java
Number n = list.get(0);
```

---

# 5. Lower Bounded Wildcard

Syntax:

```java
<? super Integer>
```

Meaning:

"Some unknown type that is Integer or one of its supertypes."

Possible types:

```text
Integer

Number

Object
```

Example:

```java
public static void addNumbers(
        List<? super Integer> list){

    list.add(10);

    list.add(20);

}
```

---

# Reading

```java
Object obj =
        list.get(0);
```

Returned type:

```text
Object
```

The compiler cannot guarantee a more specific type.

---

# Writing

Safe.

```java
list.add(100);

list.add(200);
```

---

# 6. PECS Principle

One of the most famous Java interview questions.

PECS means:

```text
Producer

Extends

Consumer

Super
```

---

# Producer

If a collection **produces** data for you to read,

use:

```java
? extends
```

Example:

```java
double sum(
    List<? extends Number> list
)
```

You only read numbers.

---

# Consumer

If a collection **consumes** data that you add,

use:

```java
? super
```

Example:

```java
void addValues(
    List<? super Integer> list
)
```

You only write integers.

---

# Easy Way to Remember

```text
Read

↓

extends

Write

↓

super
```

---

# PECS Table

| Operation | Wildcard |
|------------|----------|
| Read | ? extends |
| Write | ? super |
| Both Read & Write | Exact Type (List<T>) |

---

# 7. Covariance & Contravariance

Covariance

```text
Read-only

Uses

? extends
```

Contravariance

```text
Write-oriented

Uses

? super
```

---

# Example

Covariant

```java
List<? extends Number>
```

Can read:

```java
Number n =
    list.get(0);
```

Cannot write.

---

Contravariant

```java
List<? super Integer>
```

Can write:

```java
list.add(10);
```

Reading gives:

```java
Object
```

---

# 8. Internal Working

Wildcards exist only during compilation.

At runtime,

Java performs **Type Erasure**.

The JVM does not know whether the original type was:

```java
List<?>

List<String>

List<Integer>
```

All become ordinary `List` references after compilation.

---

# 9. Spring Boot Usage

Wildcards are common in framework APIs.

Examples:

```java
ResponseEntity<?>

List<? extends BaseEntity>

Comparator<? super T>

Class<?>

Optional<?>
```

Many Spring and JDK APIs use wildcards to make methods flexible while remaining type-safe.

---

# 10. Best Practices

✅ Use `<?>` when the type truly does not matter.

---

✅ Use `? extends` for read-only producers.

---

✅ Use `? super` for write-oriented consumers.

---

✅ Follow PECS instead of guessing.

---

# 11. Common Mistakes

❌ Assuming:

```java
List<Object>
```

can store every generic list.

It cannot.

---

❌ Trying to add elements to:

```java
List<? extends Number>
```

---

❌ Reading from:

```java
List<? super Integer>
```

expecting an `Integer`.

Only `Object` is guaranteed.

---

❌ Forgetting PECS.

---

# 12. Interview Questions

### Why is:

```java
List<Integer>

≠

List<Number>
```

---

### What is an unbounded wildcard?

---

### Difference between:

```java
<T>

and

<?>
```

---

### Difference between:

```java
? extends

and

? super
```

---

### Explain PECS.

---

### Why can't you add to:

```java
List<? extends Number>
```

---

### Why does:

```java
List<? super Integer>
```

return `Object` when reading?

---

### What is covariance?

---

### What is contravariance?

---

### Where are wildcards used in Spring?

---

# 13. Exercises

1. Write a `sum()` method using `List<? extends Number>`.
2. Write an `addNumbers()` method using `List<? super Integer>`.
3. Explain why `List<Integer>` is not a `List<Number>`.
4. Demonstrate PECS with examples.
5. Find three JDK methods that use wildcards.

---

# 14. Revision Sheet

## Wildcards

```java
<?>

<? extends T>

<? super T>
```

## PECS

Producer → Extends

Consumer → Super

## Reading

```java
? extends
```

## Writing

```java
? super
```

## Interview Focus

- Invariance
- Wildcards
- Covariance
- Contravariance
- PECS
- List<Object> vs List<?>
- Spring Framework APIs