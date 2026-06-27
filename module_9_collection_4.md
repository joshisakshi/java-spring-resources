# Module 9 - Collections (Part 4)

# HashSet, LinkedHashSet & TreeSet Deep Dive

> **Goal:** Understand how Set works, why duplicates are not allowed, and the internal implementation of HashSet, LinkedHashSet, and TreeSet.

---

# Table of Contents

1. What is Set?
2. Why Set?
3. HashSet
4. LinkedHashSet
5. TreeSet
6. Internal Working
7. HashSet vs LinkedHashSet vs TreeSet
8. Memory Representation
9. Spring Boot Usage
10. Best Practices
11. Common Mistakes
12. Interview Questions
13. Exercises
14. Revision Sheet

---

# 1. What is Set?

A **Set** is a collection that stores **unique elements**.

Characteristics:

- No duplicates
- Allows one null (HashSet, LinkedHashSet)
- No index
- Ordering depends on implementation

Example

```java
Set<String> languages = new HashSet<>();

languages.add("Java");
languages.add("Spring");
languages.add("Java");

System.out.println(languages);
```

Output

```text
[Java, Spring]
```

Duplicate "Java" is ignored.

---

# Why Do We Need Set?

Suppose we are storing registered email addresses.

Without Set

```text
abc@gmail.com

abc@gmail.com

abc@gmail.com
```

Duplicates appear.

With Set

```text
abc@gmail.com
```

Only one copy is stored.

---

# Common Real-World Examples

- Unique usernames
- Unique email IDs
- Permission names
- Tags
- User roles
- Unique product IDs

---

# 2. HashSet

HashSet is the most commonly used Set implementation.

Declaration

```java
Set<String> set = new HashSet<>();
```

Characteristics

- No duplicates
- One null allowed
- Unordered
- Fast operations
- Backed by HashMap

---

# Internal Implementation

Simplified JDK source

```java
private transient HashMap<E, Object> map;
```

HashSet stores every element as a **key** inside a HashMap.

The value is a constant dummy object.

```java
private static final Object PRESENT = new Object();
```

Internally

```text
HashMap

Java   → PRESENT

Spring → PRESENT

SQL    → PRESENT
```

Only keys matter.

---

# How add() Works

```java
set.add("Java");
```

Internally

```java
map.put("Java", PRESENT);
```

If key already exists

```java
map.put("Java", PRESENT);
```

returns previous value.

HashSet knows the element already exists and ignores it.

---

# How remove() Works

```java
set.remove("Java");
```

Internally

```java
map.remove("Java");
```

---

# Why No Duplicates?

Interview Question

Suppose

```java
set.add("Java");

set.add("Java");
```

First insertion

```text
HashMap

Java → PRESENT
```

Second insertion

Same key already exists.

HashMap replaces the value.

Since the value is always PRESENT, nothing changes.

Therefore duplicates are rejected.

---

# Time Complexity

| Operation | Complexity |
|------------|------------|
| add | O(1) Average |
| remove | O(1) Average |
| contains | O(1) Average |

Worst case

```text
O(n)
```

when many hash collisions occur.

(Java 8 improves this using Red-Black Trees.)

---

# Null Handling

```java
set.add(null);

set.add(null);
```

Only one null is stored.

---

# Iteration Order

Never assume

```java
HashSet
```

returns elements in insertion order.

Example

Inserted

```text
A

B

C
```

Output could be

```text
C

A

B
```

Order is unpredictable.

---

# 3. LinkedHashSet

LinkedHashSet extends HashSet.

Internally

```text
HashMap

+

Linked List
```

Characteristics

- Unique elements
- Preserves insertion order
- Slightly slower than HashSet

Example

```java
Set<String> set = new LinkedHashSet<>();

set.add("Java");

set.add("Spring");

set.add("SQL");
```

Output

```text
Java

Spring

SQL
```

Insertion order maintained.

---

# Internal Structure

```text
Hash Table

↓

Linked List

Java

↓

Spring

↓

SQL
```

HashMap provides fast lookup.

Linked List preserves order.

---

# Time Complexity

| Operation | Complexity |
|------------|------------|
| add | O(1) |
| remove | O(1) |
| contains | O(1) |

Slightly more memory than HashSet.

---

# 4. TreeSet

TreeSet stores elements in sorted order.

Declaration

```java
TreeSet<Integer> numbers =
        new TreeSet<>();
```

Example

```java
numbers.add(30);

numbers.add(10);

numbers.add(20);
```

Output

```text
10

20

30
```

---

# Internal Working

TreeSet is internally backed by

```text
TreeMap
```

TreeMap uses

```text
Red-Black Tree
```

Self-balancing BST.

---

# Time Complexity

| Operation | Complexity |
|------------|------------|
| add | O(log n) |
| remove | O(log n) |
| contains | O(log n) |

---

# Null Handling

Modern Java

```java
TreeSet<String> set =
        new TreeSet<>();

set.add(null);
```

Throws

```text
NullPointerException
```

Because null cannot be compared.

---

# Ordering

TreeSet uses

```text
Comparable

or

Comparator
```

to determine ordering.

We'll study these in Part 8.

---

# 5. HashSet vs LinkedHashSet vs TreeSet

| Feature | HashSet | LinkedHashSet | TreeSet |
|----------|----------|---------------|----------|
| Duplicate | No | No | No |
| Order | No | Insertion | Sorted |
| Null | One | One | No |
| Structure | HashMap | LinkedHashMap | TreeMap |
| Complexity | O(1) | O(1) | O(log n) |

---

# Memory Representation

HashSet

```text
HashMap

Bucket

↓

Node

↓

Java
```

---

LinkedHashSet

```text
Hash Table

↓

Linked List

↓

Ordered Traversal
```

---

TreeSet

```text
        20
      /    \
    10      30
```

Actually implemented as a Red-Black Tree.

---

# Spring Boot Connection

Set is commonly used for:

User Roles

```java
Set<Role> roles;
```

Authorities

```java
Set<String> permissions;
```

Unique Tags

```java
Set<String> tags;
```

Hibernate Relationships

```java
@OneToMany

private Set<Address> addresses;
```

---

# Best Practices

✅ Use HashSet when order doesn't matter.

✅ Use LinkedHashSet when insertion order matters.

✅ Use TreeSet when sorted order is required.

✅ Override both hashCode() and equals() for custom objects.

---

# Common Mistakes

❌ Assuming HashSet preserves order.

❌ Forgetting to override equals().

❌ Forgetting to override hashCode().

❌ Using TreeSet with objects that are not Comparable.

❌ Inserting null into TreeSet.

---

# Interview Questions

### How is HashSet implemented internally?

---

### Why doesn't HashSet allow duplicates?

---

### Does HashSet use hashing?

---

### Why does HashSet use HashMap?

---

### Difference between HashSet and LinkedHashSet?

---

### Difference between HashSet and TreeSet?

---

### Why is TreeSet slower?

---

### Why can't TreeSet store null?

---

### How does TreeSet sort objects?

---

### Why must equals() and hashCode() both be overridden?

(We'll study this deeply in the HashMap module.)

---

# Exercises

1. Demonstrate duplicate removal using HashSet.

2. Compare iteration order of HashSet and LinkedHashSet.

3. Store integers in TreeSet and observe sorting.

4. Create a custom class and store it in HashSet.

5. Explain why TreeSet throws NullPointerException.

---

# Revision Sheet

## Set Implementations

- HashSet
- LinkedHashSet
- TreeSet

## Internal Structures

- HashMap
- LinkedHashMap
- TreeMap

## Important Concepts

- Unique Elements
- Hashing
- Insertion Order
- Sorted Order
- Red-Black Tree

## Interview Focus

- HashSet Internals
- Duplicate Handling
- TreeSet Sorting
- LinkedHashSet Ordering
- hashCode()
- equals()