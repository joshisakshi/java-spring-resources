# Module 7 - Strings (Part 2)

# StringBuilder & StringBuffer (`07-Strings-Part2-StringBuilder-StringBuffer.md`)

> **Goal:** Understand mutable strings in Java, how `StringBuilder` and `StringBuffer` work internally, their performance characteristics, thread safety, capacity management, and when to use each.

---

# Table of Contents

1. Why StringBuilder Exists
2. Mutable vs Immutable
3. StringBuilder
4. Internal Working
5. Capacity vs Length
6. Important Methods
7. StringBuffer
8. Thread Safety
9. String vs StringBuilder vs StringBuffer
10. String Concatenation Internals
11. Spring Boot Connection
12. Interview Questions
13. Best Practices
14. Exercises
15. Revision Sheet

---

# 1. Why Do We Need StringBuilder?

Remember:

```java
String s = "Java";

s += " Spring";
s += " Boot";
s += " Interview";
```

Every concatenation creates a **new String object**.

Internally:

```text
"Java"

↓

"Java Spring"

↓

"Java Spring Boot"

↓

"Java Spring Boot Interview"
```

Four String objects are created.

This wastes:

* Memory
* CPU
* Garbage Collection effort

---

# Problem with Strings

Example

```java
String result = "";

for(int i = 0; i < 10000; i++){

    result += i;

}
```

Each iteration creates a new String.

Time complexity becomes approximately:

```text
O(n²)
```

---

# Solution

Use a mutable object.

```java
StringBuilder sb = new StringBuilder();

for(int i = 0; i < 10000; i++){

    sb.append(i);

}
```

Complexity becomes approximately:

```text
O(n)
```

---

# 2. Mutable vs Immutable

## String

Immutable

```java
String s = "Java";

s.concat("17");
```

Creates a new object.

---

## StringBuilder

Mutable

```java
StringBuilder sb = new StringBuilder("Java");

sb.append("17");
```

The same object is modified.

---

# 3. StringBuilder

Declaration

```java
public final class StringBuilder
```

Important facts:

* Mutable
* Fast
* Not thread-safe
* Introduced in Java 5

---

# Object Creation

```java
StringBuilder sb = new StringBuilder();
```

Default capacity:

```text
16
```

---

# Capacity vs Length

These are often confused.

Example

```java
StringBuilder sb = new StringBuilder();
```

```
Length = 0

Capacity = 16
```

After

```java
sb.append("Java");
```

```
Length = 4

Capacity = 16
```

Length = actual characters.

Capacity = allocated storage.

---

# Internal Working

Internally, `StringBuilder` maintains a character array.

Simplified view

```java
char[] value;
```

Memory

```text
+--------------------------------+
| J | a | v | a | _ | _ | _ | ...|
+--------------------------------+
```

Appending characters fills the array.

---

# Capacity Growth

When the array becomes full,

Java creates a larger array.

Growth formula:

```text
newCapacity = oldCapacity * 2 + 2
```

Example

```
Old Capacity = 16

↓

New Capacity = 34

↓

70

↓

142
```

This resizing strategy reduces the number of reallocations.

---

# Important Constructors

Empty

```java
StringBuilder sb = new StringBuilder();
```

---

With capacity

```java
StringBuilder sb = new StringBuilder(100);
```

Useful when the expected size is known.

---

With String

```java
StringBuilder sb = new StringBuilder("Java");
```

---

# append()

Most frequently used method.

```java
sb.append(" Spring");
```

Supports:

```java
append(int)

append(double)

append(char)

append(boolean)

append(Object)
```

Everything eventually becomes characters.

---

# insert()

```java
sb.insert(4," Boot");
```

Output

```
Java Boot
```

---

# delete()

```java
sb.delete(4,8);
```

Deletes characters from index 4 (inclusive) to 8 (exclusive).

---

# deleteCharAt()

```java
sb.deleteCharAt(2);
```

---

# replace()

```java
sb.replace(0,4,"Python");
```

---

# reverse()

```java
StringBuilder sb = new StringBuilder("Java");

sb.reverse();
```

Output

```
avaJ
```

---

# charAt()

```java
sb.charAt(2);
```

---

# setCharAt()

```java
sb.setCharAt(0,'K');
```

---

# length()

```java
sb.length();
```

Returns current character count.

---

# capacity()

```java
sb.capacity();
```

Returns allocated capacity.

---

# ensureCapacity()

Pre-allocates memory.

```java
sb.ensureCapacity(1000);
```

Useful for large concatenations.

---

# trimToSize()

Shrinks internal storage to match current length.

```java
sb.trimToSize();
```

Rarely needed.

---

# toString()

Converts the builder into an immutable String.

```java
String result = sb.toString();
```

Very common in production.

---

# 4. StringBuffer

Declaration

```java
public final class StringBuffer
```

Almost identical to `StringBuilder`.

Difference:

All important methods are synchronized.

---

Example

```java
StringBuffer sb = new StringBuffer();

sb.append("Java");
```

---

# Thread Safety

Suppose two threads call:

```java
append("A")
```

simultaneously.

With `StringBuilder`

```
Possible corruption
```

With `StringBuffer`

```
Safe
```

because methods are synchronized.

---

# Internal Synchronization

Simplified

```java
public synchronized StringBuffer append(String s){

}
```

Only one thread can execute the method at a time.

---

# Performance

| Class         | Thread Safe     | Speed                           |
| ------------- | --------------- | ------------------------------- |
| String        | Yes (Immutable) | Slow for repeated concatenation |
| StringBuilder | No              | Fastest                         |
| StringBuffer  | Yes             | Slightly slower                 |

---

# String Concatenation Internals

Example

```java
String s = "Java" + " Spring";
```

Compile-time constants are folded by the compiler.

Equivalent result:

```java
String s = "Java Spring";
```

No `StringBuilder` needed.

---

Runtime example

```java
String a = "Java";

String b = a + " Spring";
```

Compiler generates code similar to:

```java
StringBuilder sb = new StringBuilder();

sb.append(a);

sb.append(" Spring");

String b = sb.toString();
```

This optimization is done automatically by the compiler.

---

# Which One Should You Use?

| Scenario                               | Recommended   |
| -------------------------------------- | ------------- |
| Few concatenations                     | String        |
| Many concatenations (single thread)    | StringBuilder |
| Many concatenations (multiple threads) | StringBuffer  |

---

# Spring Boot Connection

Common use cases:

Building:

* SQL queries
* Log messages
* JSON responses
* Dynamic URLs
* CSV exports
* Report generation

Example

```java
StringBuilder query = new StringBuilder();

query.append("SELECT * FROM users WHERE ");
query.append("status = ?");
```

---

# Common Mistakes

❌ Using String concatenation inside loops.

❌ Using `StringBuffer` when thread safety is unnecessary.

❌ Confusing capacity with length.

❌ Forgetting to call `toString()`.

---

# Interview Questions

### Why is StringBuilder faster than String?

---

### Difference between StringBuilder and StringBuffer?

---

### What is capacity?

---

### Difference between length() and capacity()?

---

### Why does StringBuilder resize?

---

### Explain the capacity growth formula.

---

### Is StringBuilder thread-safe?

No.

---

### Why is StringBuffer slower?

Because its methods are synchronized.

---

### How does Java implement String concatenation internally?

Uses `StringBuilder` for runtime concatenation.

---

### Can StringBuilder be reused?

Yes.

Calling `setLength(0)` clears its content while retaining allocated capacity.

---

# Best Practices

* Prefer `StringBuilder` for repeated concatenation.
* Use `StringBuffer` only when true thread safety is required.
* Reserve capacity for large operations using `ensureCapacity()`.
* Avoid unnecessary conversions between `String` and `StringBuilder`.
* Use `toString()` only after all modifications are complete.

---

# Exercises

1. Reverse a String using `StringBuilder`.
2. Compare execution time of `String` vs `StringBuilder` in a loop.
3. Demonstrate capacity growth using repeated `append()`.
4. Use `insert()`, `delete()`, and `replace()` in a sample program.
5. Write a method that builds a CSV line using `StringBuilder`.

---

# Revision Sheet

* Mutable vs Immutable
* StringBuilder
* StringBuffer
* Internal `char[]`
* Capacity
* Length
* Capacity Growth
* append()
* insert()
* delete()
* replace()
* reverse()
* ensureCapacity()
* trimToSize()
* toString()
* Thread Safety
* Synchronization
* Performance Comparison
* Compiler Optimization
* Interview Questions
