# Module 9 - Collections (Part 5.1)

# HashMap Deep Dive - Part 1 (Basics & Internal Working)

> **Goal:** Understand what HashMap is, why it is so fast, how it stores data internally, and exactly what happens when we call `put()` and `get()`.

---

# Table of Contents

1. What is HashMap?
2. Why HashMap?
3. Internal Structure
4. HashMap Hierarchy
5. Node Class
6. Buckets
7. hashCode()
8. equals()
9. put() Internal Working
10. get() Internal Working
11. Memory Representation
12. Time Complexity
13. Spring Boot Usage
14. Best Practices
15. Common Mistakes
16. Interview Questions
17. Exercises
18. Revision Sheet

---

# 1. What is HashMap?

A **HashMap** is a data structure that stores data in **key-value pairs**.

Example:

```java
Map<Integer, String> students = new HashMap<>();

students.put(101, "Alice");
students.put(102, "Bob");
students.put(103, "Charlie");
```

Output conceptually:

```text
101 → Alice
102 → Bob
103 → Charlie
```

---

# Why Use HashMap?

Imagine storing employee information.

Without HashMap:

```text
Employee IDs

101
102
103
...
```

Finding employee **103** requires searching one by one.

Time Complexity:

```text
O(n)
```

With HashMap:

```text
103

↓

Hash Function

↓

Direct Bucket

↓

Employee
```

Average lookup:

```text
O(1)
```

That's why HashMap is one of the fastest data structures for lookups.

---

# Real World Examples

HashMaps are everywhere.

- User Sessions
- API Response Cache
- JWT Claims
- Configuration Properties
- HTTP Headers
- Database Row Mapping
- Shopping Cart
- Employee Lookup

Spring Boot internally uses many HashMaps.

---

# 2. HashMap Hierarchy

```text
Object
   │
AbstractMap
   │
HashMap
```

Implements:

```text
Map
Cloneable
Serializable
```

Declaration:

```java
public class HashMap<K,V>
        extends AbstractMap<K,V>
        implements Map<K,V>,
                   Cloneable,
                   Serializable
```

---

# 3. Internal Data Structure

HashMap is **not** just a single array.

Internally it uses:

```text
Array

+

Linked Lists

+

Red-Black Trees (Java 8+)
```

Initially:

```text
Bucket Array

+----+----+----+----+----+
|    |    |    |    |    |
+----+----+----+----+----+
```

Each position is called a **Bucket**.

---

# What is a Bucket?

A bucket is simply one slot in the internal array.

Example:

```text
Index

0
1
2
3
4
5
6
7
```

Each bucket may contain:

- Nothing
- One node
- Multiple nodes (collision)
- Red-Black Tree (Java 8+)

---

# Internal Field

Simplified JDK:

```java
transient Node<K,V>[] table;
```

`table` is the internal bucket array.

---

# 4. Node Class

Each bucket stores a **Node**.

Simplified OpenJDK:

```java
static class Node<K,V>
        implements Map.Entry<K,V>{

    final int hash;

    final K key;

    V value;

    Node<K,V> next;

}
```

Every node stores:

- Hash value
- Key
- Value
- Pointer to next node (for collisions)

---

# Memory Representation

Suppose:

```java
map.put(101, "Alice");
```

Memory:

```text
Bucket Array

+-----+-----+-----+-----+
|     | Node|     |     |
+-----+-----+-----+-----+

Node

Hash

Key = 101

Value = Alice

Next = null
```

---

# 5. What is hashCode()?

Every Java object inherits:

```java
public int hashCode()
```

Example:

```java
String name = "Java";

System.out.println(name.hashCode());
```

Output:

```text
2301506
```

The exact number isn't important.

The purpose is.

---

# Why hashCode()?

HashMap needs a fast way to determine **where to store a key**.

Instead of checking every bucket:

```text
Bucket 0

Bucket 1

Bucket 2

Bucket 3
```

It computes:

```text
hashCode()

↓

Bucket Index
```

---

# Bucket Index Calculation

Simplified:

```text
Bucket Index

=

hash % capacity
```

In reality, Java uses bitwise operations (covered in Part 2) because they're faster.

---

# 6. equals()

Suppose:

```java
map.put("Java", 100);

map.put("Java", 200);
```

How does HashMap know it's the same key?

Using:

```java
equals()
```

Flow:

1. Compare hash.
2. If hashes match, compare keys using `equals()`.
3. If equal, replace the value.

Result:

```text
Java → 200
```

Only the value changes.

---

# hashCode() vs equals()

| Method | Purpose |
|---------|----------|
| hashCode() | Finds bucket |
| equals() | Confirms key equality |

**Interview Rule:**

> `hashCode()` tells **where to look**.
>
> `equals()` tells **whether it's the exact key**.

---

# Why Must We Override Both?

Suppose we create:

```java
class Employee {

    int id;

}
```

Without overriding:

```java
equals()

hashCode()
```

Two employees with the same `id` may be treated as different keys.

Always override both together.

We'll revisit this in the next part with custom objects.

---

# 7. Internal Working of put()

Example:

```java
map.put(101, "Alice");
```

Execution flow:

```text
Step 1

Compute hashCode()

↓

Step 2

Calculate bucket index

↓

Step 3

Go to bucket

↓

Empty?

↓

Yes

↓

Create Node

↓

Store Node
```

Simplified JDK:

```java
put(key, value)

↓

hash(key)

↓

bucketIndex

↓

newNode()

↓

table[index] = node
```

---

# What If Key Already Exists?

Example:

```java
map.put(101, "Alice");

map.put(101, "Bob");
```

HashMap finds the existing key.

Instead of creating a new node:

```text
Update value

Alice

↓

Bob
```

Size remains the same.

---

# 8. Internal Working of get()

Suppose:

```java
map.get(101);
```

Execution:

```text
Compute hashCode()

↓

Find bucket

↓

Check first node

↓

equals()

↓

Return value
```

Average complexity:

```text
O(1)
```

---

# 9. Memory Representation

Suppose:

```java
map.put(1, "Java");

map.put(2, "Spring");

map.put(3, "SQL");
```

```text
Bucket Array

0

1 → Node(1,Java)

2 → Node(2,Spring)

3 → Node(3,SQL)

4

5

6

7
```

Each bucket stores references to Nodes.

---

# 10. Time Complexity

| Operation | Average | Worst |
|------------|---------|--------|
| put() | O(1) | O(n) |
| get() | O(1) | O(n) |
| remove() | O(1) | O(n) |
| containsKey() | O(1) | O(n) |

The worst case occurs when many keys collide into the same bucket.

Java 8 reduces this using **Red-Black Trees** (covered later).

---

# 11. Spring Boot Connection

HashMap is heavily used in Spring Boot.

Examples:

Configuration:

```java
Map<String, String> properties;
```

JWT Claims:

```java
Map<String, Object> claims;
```

REST Response:

```java
Map<String, Object> response = new HashMap<>();
```

Caching:

```java
Map<Long, User> cache;
```

Request Parameters:

```java
Map<String, String> headers;
```

---

# 12. Best Practices

✅ Use immutable keys whenever possible.

Good:

```java
String
Integer
UUID
```

Be careful with mutable objects as keys because changing them after insertion can make retrieval fail.

---

✅ Override both `equals()` and `hashCode()` for custom keys.

---

✅ Program to the `Map` interface.

```java
Map<Integer, String> map = new HashMap<>();
```

---

# 13. Common Mistakes

❌ Assuming HashMap preserves insertion order.

(It does not. Use `LinkedHashMap` if order matters.)

---

❌ Overriding `equals()` but not `hashCode()`.

---

❌ Using mutable objects as keys.

---

❌ Assuming `put()` always adds a new entry.

If the key already exists, the value is replaced.

---

# 14. Interview Questions

### What is HashMap?

---

### Why is HashMap so fast?

---

### What is a bucket?

---

### What does `hashCode()` do?

---

### What is the role of `equals()`?

---

### Difference between `hashCode()` and `equals()`?

---

### What happens internally when `put()` is called?

---

### What happens internally when `get()` is called?

---

### Why should custom keys override both methods?

---

### Does HashMap allow null keys and null values?

**Answer:**

- One `null` key
- Multiple `null` values

---

# 15. Exercises

1. Draw the internal bucket array.
2. Explain the difference between `hashCode()` and `equals()`.
3. Trace the execution of `put()` step by step.
4. Trace the execution of `get()` step by step.
5. Create a custom class and discuss why overriding `equals()` and `hashCode()` is important.

---

# 16. Revision Sheet

## Core Concepts

- HashMap
- Key-Value Pair
- Bucket
- Node
- Bucket Array

## Important Methods

- put()
- get()
- remove()
- containsKey()

## Internal Concepts

- hashCode()
- equals()
- Bucket Index
- Node

## Spring Boot Usage

- JWT Claims
- REST Responses
- Configuration
- Caching
- Request Parameters

## Interview Focus

- Bucket
- hashCode()
- equals()
- put() Flow
- get() Flow
- Custom Keys