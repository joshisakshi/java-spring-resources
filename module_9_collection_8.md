# Module 9 - Collections (Part 6)

# Hashtable & ConcurrentHashMap Deep Dive

> **Goal:** Understand why HashMap is not thread-safe, how Hashtable and ConcurrentHashMap solve concurrency problems, and when each should be used in real applications.

---

# Table of Contents

1. Why Thread Safety Matters
2. Why HashMap is Not Thread-Safe
3. Race Condition
4. Hashtable
5. Collections.synchronizedMap()
6. ConcurrentHashMap
7. Internal Working (Java 7 vs Java 8)
8. CAS (Compare-And-Swap)
9. HashMap vs Hashtable vs ConcurrentHashMap
10. Spring Boot Usage
11. Best Practices
12. Common Mistakes
13. Interview Questions
14. Exercises
15. Revision Sheet

---

# 1. Why Thread Safety Matters

Suppose two threads access the same HashMap.

```java
Map<Integer, String> map = new HashMap<>();
```

Thread A

```java
map.put(1, "Alice");
```

Thread B

```java
map.put(2, "Bob");
```

Both threads modify the same data simultaneously.

Without proper synchronization, the map can become inconsistent.

---

# What is a Race Condition?

A race condition occurs when:

- Multiple threads access shared data.
- At least one thread modifies it.
- The final result depends on the timing of execution.

Example:

```java
count = 0;
```

Thread A

```java
count++;
```

Thread B

```java
count++;
```

Expected:

```text
2
```

Possible result:

```text
1
```

because both threads read the old value before either writes the new one.

---

# 2. Why HashMap is Not Thread-Safe

HashMap performs no synchronization.

Example:

```java
Map<Integer,String> map = new HashMap<>();
```

Multiple threads can execute:

```java
put()

remove()

resize()
```

at the same time.

Possible issues:

- Lost updates
- Corrupted internal structure
- Infinite loops (older JDKs during resize)
- Incorrect reads

---

# Example

```java
Thread 1

map.put(1, "A");

Thread 2

map.put(2, "B");
```

Both threads may try to modify the same bucket simultaneously.

Result is unpredictable.

---

# 3. Hashtable

Before ConcurrentHashMap, Java provided:

```java
Hashtable<K,V>
```

Characteristics:

- Thread-safe
- Synchronized
- Slower
- Legacy class
- Does not allow null keys or null values

Example

```java
Map<Integer,String> table =
        new Hashtable<>();
```

---

# How Hashtable Achieves Thread Safety

Every public method is synchronized.

Example (simplified):

```java
public synchronized V put(K key, V value) {

    ...

}
```

Only one thread can execute `put()` on the same Hashtable instance at a time.

---

# Problem with Hashtable

Suppose:

10 threads

perform

```text
get()
```

Only one thread proceeds.

The other nine wait.

Even reads block each other.

This creates unnecessary contention and poor scalability.

---

# 4. Collections.synchronizedMap()

Java also provides a synchronized wrapper.

```java
Map<Integer,String> map =
    Collections.synchronizedMap(new HashMap<>());
```

Internally, every operation acquires a single lock.

Advantages:

- Easy to create
- Thread-safe

Disadvantages:

- Same bottleneck as Hashtable
- One global lock

---

# 5. ConcurrentHashMap

Introduced to solve Hashtable's performance problem.

Example

```java
Map<Integer,String> map =
        new ConcurrentHashMap<>();
```

Characteristics:

- Thread-safe
- High performance
- Better concurrency
- No global lock
- Does not allow null keys or null values

---

# Why is ConcurrentHashMap Faster?

Instead of locking the whole map:

```text
Hashtable

Entire Map Locked
```

ConcurrentHashMap locks only the required portion during updates.

Multiple threads can operate on different buckets simultaneously.

---

# Java 7 Internal Working

Java 7 used:

```text
Segments
```

Example:

```text
Map

↓

Segment 1

Segment 2

Segment 3

Segment 4
```

Each segment had its own lock.

Two threads could update different segments concurrently.

---

# Java 8 Improvement

Segments were removed.

Now locking happens at the bucket (bin) level.

Conceptually:

```text
Bucket 0

Bucket 1

Bucket 2

Bucket 3
```

If Thread A updates Bucket 1 and Thread B updates Bucket 3, they can proceed simultaneously.

This greatly improves throughput.

---

# 6. CAS (Compare-And-Swap)

ConcurrentHashMap also uses a lock-free technique called CAS.

CAS is an atomic CPU operation.

Idea:

```text
Current Value == Expected Value?

↓

Yes

↓

Update

↓

No

↓

Retry
```

This avoids locking for many operations.

Java implements CAS using classes from `java.util.concurrent.atomic` and low-level JVM support.

---

# Example (Conceptual)

Current value:

```text
10
```

Thread wants to change it to:

```text
20
```

CAS checks:

```text
Is current value still 10?

↓

Yes

↓

Update to 20
```

If another thread already changed it:

```text
Retry
```

---

# Read Operations

One of the biggest advantages:

Reads usually do **not** block.

Multiple threads can execute:

```java
map.get(key);
```

simultaneously.

This is why ConcurrentHashMap performs much better in read-heavy applications.

---

# Null Handling

HashMap

```java
map.put(null, "A");
```

Allowed.

ConcurrentHashMap

```java
map.put(null, "A");
```

Throws:

```text
NullPointerException
```

Reason:

`null` would make it ambiguous whether a missing value or an actual `null` value was returned during concurrent access.

---

# 7. Comparison

| Feature | HashMap | Hashtable | ConcurrentHashMap |
|----------|----------|------------|-------------------|
| Thread Safe | ❌ | ✅ | ✅ |
| Null Key | ✅ One | ❌ | ❌ |
| Null Value | ✅ Multiple | ❌ | ❌ |
| Synchronization | None | Entire Map | Bucket/CAS |
| Performance | Fast | Slow | Fast |
| Recommended Today | Yes (single-threaded) | No | Yes (multi-threaded) |

---

# 8. Spring Boot Usage

ConcurrentHashMap is commonly used for:

In-memory caches

```java
Map<Long, User> cache =
    new ConcurrentHashMap<>();
```

Session storage

Feature flags

Application metadata

Rate limit counters

Request tracking

Background job status

HashMap is typically fine for request-scoped objects that are not shared across threads.

---

# 9. Best Practices

✅ Use HashMap in single-threaded scenarios.

---

✅ Use ConcurrentHashMap for shared mutable state.

---

✅ Avoid Hashtable in new applications.

---

✅ Avoid locking the whole map unless absolutely necessary.

---

# 10. Common Mistakes

❌ Assuming HashMap is thread-safe.

---

❌ Using Hashtable in modern applications without a specific reason.

---

❌ Expecting ConcurrentHashMap to allow null keys.

---

❌ Iterating over a HashMap while another thread modifies it.

---

# 11. Interview Questions

### Why is HashMap not thread-safe?

---

### What is a race condition?

---

### Difference between Hashtable and HashMap?

---

### Difference between Hashtable and ConcurrentHashMap?

---

### Why is ConcurrentHashMap faster?

---

### What was the Segment architecture in Java 7?

---

### What changed in Java 8?

---

### What is CAS?

---

### Why doesn't ConcurrentHashMap allow null?

---

### Can multiple threads call get() simultaneously?

**Answer:** Yes, in most cases they can.

---

# 12. Exercises

1. Compare HashMap and Hashtable.
2. Explain a race condition with an example.
3. Compare Hashtable and ConcurrentHashMap.
4. Explain CAS in simple words.
5. Describe how Java 8 improved ConcurrentHashMap.

---

# 13. Revision Sheet

## Core Concepts

- Thread Safety
- Race Condition
- Synchronization
- CAS

## Collections

- HashMap
- Hashtable
- ConcurrentHashMap

## Java Versions

- Java 7 Segments
- Java 8 Bucket-Level Locking

## Spring Boot Usage

- Cache
- Session
- Metadata
- Counters

## Interview Focus

- Race Condition
- Thread Safety
- CAS
- Segment vs Bucket Locking
- HashMap vs Hashtable vs ConcurrentHashMap