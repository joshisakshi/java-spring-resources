# Module 2 - Data Types (`02-Data-Types.md`)

# Data Types in Java

> **Goal:** Understand how Java stores data in memory, the difference between primitive and reference types, wrapper classes, autoboxing, and interview-oriented concepts.

---

# Table of Contents

1. What is a Data Type?
2. Classification
3. Primitive Data Types
4. Reference Data Types
5. Memory Representation
6. Wrapper Classes
7. Autoboxing & Unboxing
8. Type Casting
9. Interview Questions
10. Best Practices
11. Summary
12. Exercises

---

# 1. What is a Data Type?

A **data type** tells Java:

* What kind of value can be stored.
* How much memory is required.
* What operations can be performed.

Example:

```java
int age = 25;
double salary = 85000.50;
char grade = 'A';
boolean isActive = true;
```

---

# 2. Classification

Java has two categories of data types.

```
Data Types
│
├── Primitive
│   ├── byte
│   ├── short
│   ├── int
│   ├── long
│   ├── float
│   ├── double
│   ├── char
│   └── boolean
│
└── Reference
    ├── String
    ├── Array
    ├── Class
    ├── Interface
    ├── Enum
    └── Objects
```

---

# 3. Primitive Data Types

Primitive types directly store values.

| Type    | Size          | Default  | Example |
| ------- | ------------- | -------- | ------- |
| byte    | 1 byte        | 0        | 100     |
| short   | 2 bytes       | 0        | 1000    |
| int     | 4 bytes       | 0        | 100000  |
| long    | 8 bytes       | 0L       | 100000L |
| float   | 4 bytes       | 0.0f     | 3.14f   |
| double  | 8 bytes       | 0.0      | 3.14159 |
| char    | 2 bytes       | '\u0000' | 'A'     |
| boolean | JVM dependent | false    | true    |

---

## byte

```java
byte age = 25;
```

Range

```
-128 to 127
```

Use Cases

* File processing
* Network packets
* Binary data

---

## short

```java
short year = 2025;
```

Range

```
-32768 to 32767
```

Rarely used in modern applications.

---

## int

Most commonly used integer type.

```java
int salary = 50000;
```

Range

```
-2^31 to 2^31 - 1
```

---

## long

Used for large numbers.

```java
long population = 1400000000L;
```

Notice the suffix:

```
L
```

Without it, Java treats the number as an int.

---

## float

Single precision.

```java
float pi = 3.14f;
```

Notice

```
f
```

is mandatory.

---

## double

Preferred for decimal values.

```java
double price = 99.99;
```

Most Java applications use `double` instead of `float`.

---

## char

Stores a Unicode character.

```java
char grade = 'A';
char symbol = '$';
```

A `char` occupies **2 bytes** because Java uses Unicode.

---

## boolean

Stores only two values.

```java
boolean loggedIn = true;
```

---

# 4. Reference Data Types

Reference variables store the **address (reference)** of an object, not the object itself.

```java
String name = "Sakshi";
```

Memory

```
Stack

name
 │
 ▼

Heap

" Sakshi "
```

Examples

```java
String

Employee

ArrayList

HashMap

User
```

---

# Primitive vs Reference

| Primitive      | Reference         |
| -------------- | ----------------- |
| Stores value   | Stores address    |
| Fixed size     | Depends on object |
| Faster         | Slightly slower   |
| Cannot be null | Can be null       |
| Lower memory   | Higher memory     |

---

# Memory Representation

Primitive

```
Stack

age = 25
```

Reference

```
Stack

emp
 │
 ▼

Heap

Employee Object
```

---

# 5. Wrapper Classes

Every primitive has a corresponding object.

| Primitive | Wrapper   |
| --------- | --------- |
| byte      | Byte      |
| short     | Short     |
| int       | Integer   |
| long      | Long      |
| float     | Float     |
| double    | Double    |
| char      | Character |
| boolean   | Boolean   |

Example

```java
Integer age = 25;

Double salary = 55000.0;

Character ch = 'A';
```

---

## Why Wrapper Classes?

Collections store **objects**, not primitives.

Invalid

```java
ArrayList<int> list;
```

Valid

```java
ArrayList<Integer> list = new ArrayList<>();
```

---

# 6. Autoboxing

Automatic conversion from primitive to wrapper.

```java
int age = 25;

Integer obj = age;
```

Compiler internally performs

```java
Integer obj = Integer.valueOf(age);
```

---

# 7. Unboxing

Wrapper to primitive.

```java
Integer age = 25;

int value = age;
```

Compiler internally

```java
int value = age.intValue();
```

---

# 8. Type Casting

## Widening (Implicit)

Smaller → Larger

```
byte

↓

short

↓

int

↓

long

↓

float

↓

double
```

Example

```java
int x = 10;

double y = x;
```

No data loss.

---

## Narrowing (Explicit)

```java
double x = 99.8;

int y = (int) x;
```

Output

```
99
```

Fraction is discarded.

---

# Default Values

| Type      | Default  |
| --------- | -------- |
| byte      | 0        |
| short     | 0        |
| int       | 0        |
| long      | 0L       |
| float     | 0.0f     |
| double    | 0.0      |
| char      | '\u0000' |
| boolean   | false    |
| Reference | null     |

Default values apply to **instance variables**, not local variables.

---

# Frequently Asked Interview Questions

## Q1 Why is String not a primitive?

Because String is an object with methods such as:

```java
length()

substring()

charAt()

toUpperCase()
```

---

## Q2 Why are wrapper classes needed?

* Collections
* Generics
* Frameworks
* Nullable values
* Utility methods

---

## Q3 Difference between int and Integer?

| int            | Integer         |
| -------------- | --------------- |
| Primitive      | Object          |
| Faster         | Slightly slower |
| Cannot be null | Can be null     |
| Less memory    | More memory     |

---

## Q4 Which is faster?

```
int
```

Because no object creation occurs.

---

## Q5 Why can't ArrayList store int?

Because Generics work only with objects.

---

## Q6 Explain Autoboxing.

Automatic conversion between primitive and wrapper classes introduced in Java 5.

---

# Production Usage

Example

```java
class Employee {

    private Long id;

    private String name;

    private Double salary;

}
```

Spring Boot entities frequently use wrapper classes because:

* Database columns may contain NULL.
* Frameworks expect objects.
* Hibernate works with object references.

---

# Best Practices

✅ Use `int` for normal integers.

✅ Use `long` for IDs and timestamps.

✅ Use `double` for decimal calculations.

✅ Use wrapper classes when null values are possible.

✅ Prefer primitives in performance-critical code.

---

# Common Mistakes

❌ Forgetting `L`

```java
long x = 10000000000;
```

Correct

```java
long x = 10000000000L;
```

---

❌ Forgetting `f`

```java
float pi = 3.14;
```

Correct

```java
float pi = 3.14f;
```

---

❌ Comparing Integer objects using `==`

Wrong

```java
Integer a = 200;
Integer b = 200;

System.out.println(a == b);
```

Correct

```java
System.out.println(a.equals(b));
```

---

# Summary

* Java has Primitive and Reference data types.
* Primitive variables store actual values.
* Reference variables store object addresses.
* Wrapper classes are object representations of primitives.
* Autoboxing converts primitive → object.
* Unboxing converts object → primitive.
* Widening is automatic.
* Narrowing requires explicit casting.

---

# Exercises

1. Difference between `int` and `Integer`.
2. Explain Autoboxing with an example.
3. Why can't Generics use primitive types?
4. Explain widening and narrowing conversions.
5. What happens when an `Integer` variable is `null` and gets unboxed?
6. Why is `double` preferred over `float` in backend applications?

---

# Revision Sheet

* 8 Primitive Types
* Wrapper Classes
* Primitive vs Reference
* Stack vs Heap
* Autoboxing
* Unboxing
* Widening
* Narrowing
* Default Values
* Interview Questions
