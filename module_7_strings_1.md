# Module 7 - Strings (Part 1)

# String Internals, String Pool & Immutability (`07-Strings-Part1-String-Internals.md`)

> **Goal:** Master Java Strings by understanding their internal implementation, String Constant Pool, immutability, memory allocation, `intern()`, and common interview questions.

---

# Table of Contents

1. Introduction to Strings
2. Why String is Special
3. String Class
4. String Creation
5. Heap vs String Pool
6. String Constant Pool (SCP)
7. String Immutability
8. `new String()` vs String Literal
9. `==` vs `equals()`
10. `intern()`
11. Why String is `final`
12. Memory Diagrams
13. Spring Boot Connection
14. Interview Questions
15. Best Practices
16. Exercises
17. Revision Sheet

---

# 1. What is a String?

A **String** is a sequence of characters used to represent text.

Unlike primitive types, `String` is a **class** in Java.

```java
String name = "Sakshi";
```

Internally:

```java
String name = new String("Sakshi");
```

creates a `String` object (with some optimizations when using literals).

---

# 2. Why is String Special?

Strings are the **most frequently used objects** in Java.

Examples:

* Usernames
* Passwords
* URLs
* HTTP Headers
* JSON Keys
* SQL Queries
* File Paths
* XML
* JWT Tokens
* API Requests & Responses

Java therefore gives Strings special treatment for performance.

---

# 3. String Class

Declaration (simplified):

```java
public final class String
```

Important observations:

* It is a class.
* It is `final`.
* It is immutable.
* It implements:

  * `Serializable`
  * `Comparable<String>`
  * `CharSequence`

---

# 4. Ways to Create Strings

## Using a String Literal

```java
String s1 = "Java";
```

Stored in the **String Constant Pool**.

---

## Using `new`

```java
String s2 = new String("Java");
```

Creates a new object in the Heap.

---

## From a Character Array

```java
char[] letters = {'J', 'a', 'v', 'a'};

String s = new String(letters);
```

---

## From a Byte Array

```java
byte[] bytes = {72, 101, 108, 108, 111};

String s = new String(bytes);
```

---

# 5. String Constant Pool (SCP)

The **String Constant Pool** is a special memory area inside the Heap that stores unique String literals.

Purpose:

* Avoid duplicate String objects.
* Save memory.
* Improve performance.

---

Example

```java
String s1 = "Java";
String s2 = "Java";
```

Memory

```text
Stack

s1 ─────┐
         │
s2 ──────┘
         │
         ▼

String Pool

+--------+
| "Java" |
+--------+
```

Only **one object** is created.

---

# 6. Heap vs String Pool

```java
String s1 = "Java";

String s2 = new String("Java");
```

Memory

```text
Stack

s1 ───────► Pool Object ("Java")

s2 ───────► Heap Object
                 │
                 ▼
             Pool Object ("Java")
```

Two different objects exist:

* Heap Object
* Pool Object

---

# 7. How JVM Handles a String Literal

```java
String s = "Spring";
```

Execution Steps:

1. JVM checks the String Pool.
2. If `"Spring"` exists, reuse it.
3. Otherwise, create a new pooled String.
4. Store the reference in `s`.

---

# 8. Why Does the Pool Exist?

Without pooling:

```java
String a = "Java";
String b = "Java";
String c = "Java";
```

Three identical objects would waste memory.

With pooling:

All references point to the same object.

---

# 9. String Immutability

A `String` object **cannot be changed after it is created**.

Example

```java
String s = "Java";

s.concat(" 17");
```

Output

```java
Java
```

The original String remains unchanged.

Correct

```java
s = s.concat(" 17");
```

Now

```java
Java 17
```

---

# What Happens Internally?

```java
String s = "Java";
```

```
Object A

"Java"
```

After

```java
s = s.concat(" 17");
```

```
Object A

"Java"

Object B

"Java 17"
```

Reference `s` now points to Object B.

Object A remains unchanged.

---

# 10. Why are Strings Immutable?

This is a favorite interview question.

## Security

Database URLs

```text
jdbc:mysql://localhost
```

should never change unexpectedly.

---

## Thread Safety

Multiple threads can safely share the same String object.

No synchronization is required.

---

## HashMap Optimization

Hash code is cached because the content never changes.

This makes HashMap lookups faster.

---

## String Pool

Pooling is only possible because Strings cannot change.

Otherwise:

```java
String a = "Java";
String b = "Java";
```

If `a` changed to `"Python"`,

`b` would also appear changed.

---

# 11. Why is String `final`?

```java
public final class String
```

Reasons:

* Prevent subclass modification.
* Maintain immutability.
* Ensure security.
* Preserve String Pool correctness.

---

# 12. `==` vs `equals()`

## `==`

Compares references.

```java
String a = "Java";
String b = "Java";

System.out.println(a == b);
```

Output

```text
true
```

Both point to the same pooled object.

---

Example

```java
String a = new String("Java");
String b = new String("Java");

System.out.println(a == b);
```

Output

```text
false
```

Different Heap objects.

---

## `equals()`

Compares content.

```java
String a = new String("Java");
String b = new String("Java");

System.out.println(a.equals(b));
```

Output

```text
true
```

---

# 13. `intern()`

Returns the pooled version of a String.

```java
String s1 = new String("Java");

String s2 = s1.intern();
```

Now `s2` points to the String Pool object.

---

# When Should You Use `intern()`?

Rarely in modern applications.

Useful only when:

* Millions of repeated Strings.
* Memory optimization is important.

---

# 14. Common String Operations Create New Objects

```java
String s = "Java";

s.toUpperCase();

s.replace("J", "K");

s.concat(" 17");

s.substring(1);
```

Every operation creates a new String.

---

# 15. Memory Diagram

```java
String a = "Java";
String b = "Java";
String c = new String("Java");
```

```
Stack

a ─────┐
        │
b ──────┘
        │
        ▼

String Pool

+--------+
| "Java" |
+--------+

Heap

+--------+
| "Java" |
+--------+

c ─────────► Heap Object
```

---

# 16. Spring Boot Connection

Strings appear everywhere:

* REST URLs
* JSON payloads
* HTTP Headers
* DTO fields
* SQL queries
* Environment variables
* Configuration properties
* JWT tokens
* Logging
* Request parameters

Understanding immutability helps prevent accidental data modification across threads in web applications.

---

# Common Mistakes

❌ Using `==` to compare Strings.

```java
a == b
```

Instead

```java
a.equals(b)
```

---

❌ Assuming `concat()` modifies the original String.

---

❌ Creating unnecessary Strings using `new`.

Prefer

```java
String s = "Java";
```

instead of

```java
String s = new String("Java");
```

---

# Interview Questions

### Why are Strings immutable?

---

### Why is String `final`?

---

### What is the String Constant Pool?

---

### Difference between `new String()` and String literals?

---

### Difference between `==` and `equals()`?

---

### What does `intern()` do?

---

### Why does Java maintain a String Pool?

---

### Can a String object ever change?

No. Operations create new objects.

---

### Where is the String Pool stored?

Since Java 7, the String Pool resides in the **Heap** (earlier JVM versions stored it in the PermGen area).

---

### Why are Strings heavily used as HashMap keys?

Because they are immutable and cache their hash code.

---

# Best Practices

* Prefer String literals over `new String()`.
* Always use `equals()` for content comparison.
* Avoid excessive String concatenation inside loops.
* Use `StringBuilder` for mutable text (covered in Part 2).
* Treat Strings as immutable values.

---

# Exercises

1. Draw the memory diagram for:

   ```java
   String a = "Hello";
   String b = "Hello";
   String c = new String("Hello");
   ```
2. Explain why `==` and `equals()` produce different results.
3. Demonstrate String immutability using `concat()`.
4. Use `intern()` and explain the memory changes.
5. List five reasons why Strings are immutable.

---

# Revision Sheet

* String Class
* String Literals
* `new String()`
* String Constant Pool
* Heap vs Pool
* Immutability
* `final`
* `==`
* `equals()`
* `intern()`
* Memory Diagrams
* Spring Boot Usage
* Interview Questions
