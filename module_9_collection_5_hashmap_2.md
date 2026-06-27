# Module 9 - Collections (Part 5.2)

# HashMap Deep Dive - Part 2 (Collisions, Load Factor & Rehashing)

> **Goal:** Understand how HashMap handles collisions, why its default capacity is 16, why the load factor is 0.75, how resizing works, and why HashMap uses powers of two.

---

# Table of Contents

1. Hash Collision
2. Collision Handling
3. Separate Chaining
4. Capacity
5. Load Factor
6. Threshold
7. Resize & Rehash
8. Why Capacity is Power of Two
9. Bucket Index Calculation
10. Internal Working
11. Memory Representation
12. Time Complexity
13. Spring Boot Connection
14. Best Practices
15. Common Mistakes
16. Interview Questions
17. Exercises
18. Revision Sheet

---

# 1. What is a Hash Collision?

A collision occurs when **two different keys map to the same bucket**.

Example:

```text
Capacity = 8

hash("Apple")  -> Bucket 3

hash("Orange") -> Bucket 3
```

Both keys want to occupy bucket **3**.

This is called a **Hash Collision**.

---

# Why Do Collisions Happen?

HashMap has a **finite number of buckets**.

Imagine:

```text
10,000 keys

↓

16 buckets
```

Some keys must share the same bucket.

Collisions are **normal** and expected.

A good hash function minimizes collisions but cannot eliminate them.

---

# 2. Collision Handling

Java uses **Separate Chaining**.

Instead of rejecting the second key, HashMap stores multiple nodes in the same bucket.

Example:

```text
Bucket 5

↓

+--------------------+
| Key = Apple        |
| Value = 10         |
+--------------------+
          │
          ▼
+--------------------+
| Key = Orange       |
| Value = 20         |
+--------------------+
          │
          ▼
+--------------------+
| Key = Mango        |
| Value = 30         |
+--------------------+
```

This linked list is called a **chain**.

---

# 3. How put() Works During Collision

Suppose:

```java
map.put("Apple", 10);

map.put("Orange", 20);
```

Both hash to bucket 5.

Execution:

```text
Find Bucket

↓

Bucket Empty?

↓

No

↓

Traverse Linked List

↓

Same Key?

↓

No

↓

Append New Node
```

If the key already exists:

```text
Replace Value

↓

Done
```

No new node is created.

---

# 4. Capacity

Capacity = number of buckets.

Default capacity:

```java
16
```

Internally:

```text
Bucket Array

0
1
2
3
...
15
```

16 buckets.

---

# Can Capacity be Specified?

Yes.

```java
Map<Integer,String> map =
        new HashMap<>(64);
```

Now:

```text
Capacity = 64
```

Useful when you know you'll store many entries.

---

# 5. Load Factor

The **Load Factor** determines when HashMap should resize.

Default:

```text
0.75
```

Meaning:

Resize when the map becomes **75% full**.

---

# Why 0.75?

This is one of the most common interview questions.

Trade-off:

```text
Small Load Factor

↓

Less Collision

↓

More Memory
```

Large Load Factor:

```text
More Collision

↓

Less Memory

↓

Slower Lookup
```

After years of benchmarking, Java chose:

```text
0.75
```

because it balances:

- Speed
- Memory
- Collision rate

---

# 6. Threshold

Threshold determines **when resizing occurs**.

Formula:

```text
Threshold

=

Capacity × Load Factor
```

Example:

```text
Capacity = 16

Load Factor = 0.75

Threshold = 12
```

After inserting the **13th element**, resizing begins.

---

# Another Example

```text
Capacity = 64

Threshold =

64 × 0.75

=

48
```

The 49th insertion triggers resizing.

---

# 7. Resize (Rehashing)

Suppose:

```text
Capacity = 16

Threshold = 12
```

After adding the 13th element:

```text
Resize

↓

Capacity = 32
```

HashMap does **not** simply copy the array.

It performs **Rehashing**.

---

# What is Rehashing?

Every node's bucket is recalculated.

Old:

```text
Capacity = 16

↓

Bucket 5
```

New:

```text
Capacity = 32

↓

Maybe Bucket 5

Maybe Bucket 21
```

Some nodes stay.

Some move.

---

# Internal Flow

```text
Old Bucket Array

↓

Create New Array

↓

Double Capacity

↓

Recalculate Bucket

↓

Move Every Node
```

---

# Simplified JDK Logic

```java
resize()

↓

newCapacity = oldCapacity * 2;

↓

Create New Bucket Array

↓

Move Every Node

↓

Replace Old Table
```

---

# Why is Resize Expensive?

Suppose:

```text
500,000 entries
```

All entries must be redistributed.

Complexity:

```text
O(n)
```

Fortunately, resizing happens infrequently.

---

# 8. Why Capacity is Always a Power of Two

Interview Question ⭐⭐⭐⭐⭐

Possible capacities:

```text
16

32

64

128

256
```

Never:

```text
30

50

75
```

Why?

Because HashMap calculates bucket indices using **bitwise AND**.

---

# 9. Bucket Index Calculation

Many tutorials say:

```text
hash % capacity
```

Actually, Java uses:

```java
index = (capacity - 1) & hash;
```

Example:

```text
Capacity = 16

↓

Capacity - 1 = 15
```

Binary:

```text
1111
```

AND operation is much faster than modulo (`%`).

This optimization works correctly **only when capacity is a power of two**.

---

# Why Bitwise AND?

Advantages:

- Faster than modulo
- Better CPU optimization
- Better bucket distribution

That's why HashMap capacities are:

```text
16

32

64

128
```

---

# Memory Representation Before Resize

```text
Capacity = 4

Buckets

0

1

2

3
```

After resize:

```text
Capacity = 8

Buckets

0

1

2

3

4

5

6

7
```

Some nodes move to new buckets.

---

# 10. Time Complexity

| Operation | Average | Worst |
|------------|----------|--------|
| put() | O(1) | O(n) |
| get() | O(1) | O(n) |
| remove() | O(1) | O(n) |
| resize() | O(n) | O(n) |

---

# 11. Spring Boot Connection

Large Spring Boot applications often store:

- Cache entries
- JWT claims
- Request metadata
- Session objects

If HashMaps resize frequently, performance suffers.

Pre-sizing large maps can improve performance.

Example:

```java
Map<Long, User> cache =
        new HashMap<>(5000);
```

---

# 12. Best Practices

✅ Specify initial capacity when approximate size is known.

---

✅ Use immutable keys.

---

✅ Understand that collisions are normal.

---

✅ Don't fear resizing—it is optimized.

---

# 13. Common Mistakes

❌ Thinking collisions are errors.

---

❌ Assuming resize simply copies the array.

---

❌ Forgetting that rehashing recalculates bucket positions.

---

❌ Thinking HashMap uses `%` internally.

Modern Java uses bitwise `&`.

---

# 14. Interview Questions

### What is a hash collision?

---

### How does HashMap resolve collisions?

---

### What is Separate Chaining?

---

### What is the default capacity?

**16**

---

### What is the default load factor?

**0.75**

---

### Why was 0.75 chosen?

---

### What is the threshold?

---

### What triggers resizing?

---

### What is rehashing?

---

### Why is resizing expensive?

---

### Why are capacities powers of two?

---

### Why does HashMap use bitwise AND instead of modulo?

---

# 15. Exercises

1. Explain a hash collision with a diagram.
2. Calculate threshold for capacities 16, 32, and 64.
3. Explain rehashing step by step.
4. Explain why resizing is O(n).
5. Explain why capacities are powers of two.

---

# 16. Revision Sheet

## Core Concepts

- Collision
- Bucket
- Separate Chaining
- Capacity
- Load Factor
- Threshold
- Resize
- Rehash

## Important Values

- Default Capacity = 16
- Default Load Factor = 0.75

## Internal Concepts

- Bucket Redistribution
- Power of Two
- Bitwise AND
- Threshold Calculation

## Interview Focus

- Collision Handling
- Load Factor
- Rehashing
- Resize
- Bitwise Bucket Calculation
- Capacity Growth