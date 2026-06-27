# Module 9 - Collections Framework (Part 1)

# Collections Framework Overview

> **Goal:** Understand the Java Collections Framework, why it exists, its hierarchy, core interfaces, internal working, performance overview, and its importance in Spring Boot applications.

---

# Table of Contents

1. Introduction
2. Why Collections?
3. Arrays vs Collections
4. Collections Framework Hierarchy
5. Core Interfaces
6. Iterable & Iterator
7. Collection vs Collections
8. Internal Working
9. Time Complexity Overview
10. Memory Representation
11. Spring Boot Connection
12. Best Practices
13. Common Mistakes
14. Interview Questions
15. Exercises
16. Revision Sheet

---

# 1. What is the Java Collections Framework?

The **Java Collections Framework (JCF)** is a unified architecture provided by Java to store, manipulate, search, sort, and process groups of objects efficiently.

Instead of creating custom data structures every time, Java provides ready-made implementations like:

- ArrayList
- LinkedList
- HashSet
- TreeSet
- HashMap
- PriorityQueue

These implementations are optimized, well-tested, and widely used in production systems.

---

# Why was Collections Framework Introduced?

Before Java Collections, developers primarily used arrays.

Example:

```java
String user1 = "Alice";
String user2 = "Bob";
String user3 = "Charlie";
```

Problems:

- Fixed size
- Difficult insertion/deletion
- No sorting utilities
- No searching utilities
- No generic programming

Collections solve all of these problems.

---

# Real World Examples

Collections are used everywhere:

- Shopping cart
- List of employees
- Cache
- Session management
- API responses
- Database query results
- Authentication roles
- Configuration properties

---

# 2. Arrays vs Collections

| Feature | Array | Collections |
|----------|--------|------------|
| Size | Fixed | Dynamic |
| Stores | Primitive + Objects | Objects (Autoboxing supports primitives) |
| Generics | ❌ | ✅ |
| Utility Methods | Very Few | Rich API |
| Sorting | Manual / Arrays.sort() | Collections.sort() |
| Searching | Manual | Utility Methods |
| Memory | Slightly Less | Slightly More |

---

# Advantages of Collections

- Dynamic size
- Generic support
- Built-in algorithms
- Better readability
- Reusable implementations
- Supports multiple data structures

---

# 3. Collections Framework Hierarchy

```text
                        Iterable
                            │
                       Collection
          ┌─────────────────┼─────────────────┐
          │                 │                 │
         List              Set             Queue
          │                 │                 │
 ┌────────┼───────┐    ┌────┼────────┐        │
 │        │       │    │    │        │        │
ArrayList LinkedList Vector HashSet LinkedHashSet TreeSet
                                           │
                                    PriorityQueue
                                    ArrayDeque

---------------------------------------------

Map (Separate Hierarchy)

Map
│
├── HashMap
├── LinkedHashMap
├── TreeMap
├── Hashtable
└── ConcurrentHashMap
```

---

# Why is Map Separate?

**Interview Question**

The Collection interface stores **individual elements**.

Example:

```java
List<String> names;
```

A Map stores **key-value pairs**.

```java
Map<Integer, String> users;
```

Since Map does not represent a collection of single elements, it is not part of the Collection hierarchy.

---

# 4. Core Interfaces

## Collection

Root interface of the Collections Framework.

Common methods:

```java
add()
remove()
contains()
size()
clear()
iterator()
isEmpty()
```

---

## List

Characteristics:

- Ordered
- Allows duplicates
- Index based
- Preserves insertion order

Implementations:

- ArrayList
- LinkedList
- Vector

Example:

```java
List<String> fruits = new ArrayList<>();

fruits.add("Apple");
fruits.add("Apple");
fruits.add("Mango");
```

Duplicates are allowed.

---

## Set

Characteristics:

- No duplicates
- No indexing
- Ordering depends on implementation

Implementations:

- HashSet
- LinkedHashSet
- TreeSet

Example:

```java
Set<String> names = new HashSet<>();

names.add("Java");
names.add("Java");
```

Only one "Java" is stored.

---

## Queue

Represents FIFO (First In First Out).

Common methods:

```java
offer()

poll()

peek()
```

Implementations:

- PriorityQueue
- ArrayDeque
- LinkedList

---

## Map

Stores

```text
Key → Value
```

Example:

```java
Map<Integer,String> students = new HashMap<>();

students.put(1,"Alice");

students.put(2,"Bob");
```

Keys must be unique.

Values may repeat.

---

# 5. Iterable

Every Collection implements Iterable.

This enables enhanced for-loops.

Example:

```java
for(String fruit : fruits){

    System.out.println(fruit);

}
```

Without Iterable, enhanced for-loops would not work.

---

# Iterator

Iterator is used to traverse collections.

Methods:

```java
hasNext()

next()

remove()
```

Example:

```java
Iterator<String> iterator = fruits.iterator();

while(iterator.hasNext()){

    System.out.println(iterator.next());

}
```

---

# Why Iterator?

Different collections have different internal structures.

Iterator provides a common traversal mechanism.

Example:

- ArrayList → Array
- LinkedList → Nodes
- HashSet → Hash Table

Your code remains the same.

---

# 6. Collection vs Collections

Many interviewees confuse these.

## Collection

Interface

Example:

```java
Collection<String> collection;
```

---

## Collections

Utility class containing static methods.

Example:

```java
Collections.sort(list);

Collections.reverse(list);

Collections.shuffle(list);

Collections.max(list);

Collections.min(list);
```

---

# 7. Internal Working

Suppose we write:

```java
List<String> list = new ArrayList<>();
```

Memory:

```text
Stack

list
 │
 ▼

Heap

ArrayList Object
        │
        ▼

Object[]

+--------+--------+--------+
| Ref A  | Ref B  | Ref C  |
+--------+--------+--------+
```

Collections themselves store **references**, not actual objects.

---

# Programming to Interfaces

Recommended:

```java
List<String> list = new ArrayList<>();
```

Not

```java
ArrayList<String> list = new ArrayList<>();
```

Why?

Tomorrow you can replace:

```java
ArrayList
```

with

```java
LinkedList
```

without changing client code.

Example:

```java
List<String> list = new LinkedList<>();
```

This follows the **Dependency Inversion Principle (SOLID)**.

---

# 8. Time Complexity Overview

| Collection | Search | Insert | Delete | Access |
|------------|--------|--------|--------|--------|
| ArrayList | O(n) | O(1)* | O(n) | O(1) |
| LinkedList | O(n) | O(1)** | O(1)** | O(n) |
| HashSet | O(1) Avg | O(1) Avg | O(1) Avg | N/A |
| TreeSet | O(log n) | O(log n) | O(log n) | N/A |
| HashMap | O(1) Avg | O(1) Avg | O(1) Avg | O(1) Avg |
| TreeMap | O(log n) | O(log n) | O(log n) | O(log n) |

\* At the end (amortized)

\** If node reference is already available

---

# 9. Spring Boot Connection

Collections are everywhere in Spring Boot.

Examples:

Repository

```java
List<User> findAll();
```

Controller

```java
@GetMapping("/users")

public List<UserDto> getUsers(){

}
```

Configuration

```java
Map<String,String>
```

Security

```java
Set<String> roles;
```

Request Parameters

```java
List<Long> ids
```

Almost every REST API returns collections.

---

# 10. Best Practices

✅ Program to interfaces.

```java
List<User> users = new ArrayList<>();
```

✅ Use Generics.

```java
List<String> names;
```

✅ Choose the correct collection.

Example:

- Fast lookup → HashMap
- Ordered list → ArrayList
- Unique values → HashSet
- Sorted values → TreeSet

---

# 11. Common Mistakes

❌ Using raw collections.

```java
List list = new ArrayList();
```

Correct

```java
List<String> list = new ArrayList<>();
```

---

❌ Assuming HashMap preserves insertion order.

---

❌ Assuming HashSet stores duplicates.

---

❌ Choosing LinkedList without understanding trade-offs.

---

# 12. Interview Questions

### What is the Java Collections Framework?

---

### Why were Collections introduced?

---

### Difference between Arrays and Collections?

---

### Difference between Collection and Collections?

---

### Why is Map not part of Collection?

---

### Why should we program to interfaces?

---

### Explain Iterable.

---

### Explain Iterator.

---

### Why does every Collection implement Iterable?

---

### Name some Collection implementations used in Spring Boot.

---

# 13. Exercises

1. Draw the Collections hierarchy from memory.

2. Explain why Map has a separate hierarchy.

3. Write examples of List, Set, Queue, and Map.

4. Explain the difference between Collection and Collections.

5. Explain why programming to interfaces is recommended.

---

# 14. Revision Sheet

## Core Concepts

- Collections Framework
- Arrays vs Collections
- Dynamic Collections
- Collection Interface
- Iterable
- Iterator

## Interfaces

- List
- Set
- Queue
- Map

## Utility

- Collections Class

## Principles

- Programming to Interfaces
- Loose Coupling

## Spring Boot Usage

- Repository Results
- REST APIs
- DTO Lists
- Request Parameters
- Security Roles
- Configuration

## Interview Focus

- Arrays vs Collections
- Collection vs Collections
- Iterable vs Iterator
- Why Map is separate
- Programming to Interfaces

---

# Next Module

**Module 9 - Part 2: ArrayList Deep Dive**

We'll cover:

- Internal Array Structure
- Capacity vs Size
- Growth Formula
- add()
- remove()
- get()
- set()
- contains()
- ensureCapacity()
- trimToSize()
- Array Copy Mechanism
- Resizing Algorithm
- Memory Diagrams
- Amortized Analysis
- Fail Fast Iterator
- RandomAccess Interface
- Source Code Walkthrough
- Spring Boot Usage
- Interview Questions