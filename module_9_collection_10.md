# Module 9 - Collections (Part 10)

# Advanced Maps & Collection Selection Guide

> **Goal:** Master specialized Map implementations, immutable collections, LRU cache concepts, and learn how to choose the right collection for different scenarios.

---

# Table of Contents

1. LinkedHashMap
2. WeakHashMap
3. IdentityHashMap
4. EnumMap
5. Immutable Collections (Java 9+)
6. LRU Cache
7. Choosing the Right Collection
8. Collections Cheat Sheet
9. Spring Boot Usage
10. Best Practices
11. Common Mistakes
12. Interview Questions
13. Exercises
14. Revision Sheet

---

# 1. LinkedHashMap

`LinkedHashMap` extends `HashMap` and maintains a predictable iteration order.

Internally it combines:

```text
Hash Table

+

Doubly Linked List
```

Example:

```java
Map<Integer, String> map = new LinkedHashMap<>();

map.put(3, "C");
map.put(1, "A");
map.put(2, "B");

System.out.println(map);
```

Output:

```text
{3=C, 1=A, 2=B}
```

The insertion order is preserved.

---

## Access Order

You can also maintain **access order** instead of insertion order.

```java
Map<Integer, String> map =
    new LinkedHashMap<>(16, 0.75f, true);
```

Now, whenever an entry is accessed using `get()`, it moves to the end of the linked list.

This feature is commonly used for LRU caches.

---

## Complexity

| Operation | Complexity |
|-----------|------------|
| put() | O(1) |
| get() | O(1) |
| remove() | O(1) |

---

# 2. WeakHashMap

`WeakHashMap` stores **weak references** to its keys.

If a key is no longer strongly referenced elsewhere, it becomes eligible for garbage collection.

Example:

```java
Map<Object, String> map =
    new WeakHashMap<>();
```

This is useful for:

- Metadata caches
- Temporary mappings
- Memory-sensitive applications

Unlike `HashMap`, entries may disappear automatically after garbage collection.

---

# 3. IdentityHashMap

Unlike HashMap, `IdentityHashMap` compares keys using:

```java
==
```

instead of:

```java
equals()
```

Example:

```java
String a = new String("Java");
String b = new String("Java");

Map<String, Integer> map =
    new IdentityHashMap<>();

map.put(a, 1);
map.put(b, 2);
```

Result:

```text
Size = 2
```

because `a` and `b` are different object references.

Typical use cases are rare and include object graph traversal and framework internals.

---

# 4. EnumMap

Optimized for enum keys.

Example:

```java
enum Status {
    NEW,
    ACTIVE,
    CLOSED
}

Map<Status, String> map =
    new EnumMap<>(Status.class);
```

Advantages:

- Faster than HashMap for enum keys
- Compact memory usage
- Natural enum ordering

---

# 5. Immutable Collections (Java 9+)

Java 9 introduced factory methods for immutable collections.

```java
List<String> list =
    List.of("Java", "Spring");

Set<String> set =
    Set.of("A", "B");

Map<Integer, String> map =
    Map.of(
        1, "One",
        2, "Two"
    );
```

Attempting to modify them throws:

```text
UnsupportedOperationException
```

---

## Difference from Unmodifiable Wrappers

```java
Collections.unmodifiableList(original)
```

creates a **read-only view** of an existing collection.

```java
List.of(...)
```

creates a new immutable collection.

---

# 6. LRU Cache

LRU stands for:

```text
Least Recently Used
```

The least recently accessed entry is removed first.

LinkedHashMap provides built-in support.

Example:

```java
class LRUCache<K,V>
        extends LinkedHashMap<K,V>{

    private final int capacity;

    public LRUCache(int capacity){

        super(capacity,0.75f,true);

        this.capacity = capacity;
    }

    @Override
    protected boolean removeEldestEntry(
            Map.Entry<K,V> eldest){

        return size() > capacity;
    }

}
```

This is a popular interview question.

---

# 7. Choosing the Right Collection

| Requirement | Recommended Collection |
|-------------|------------------------|
| Fast lookup | HashMap |
| Preserve insertion order | LinkedHashMap |
| Sorted keys | TreeMap |
| Thread-safe map | ConcurrentHashMap |
| Enum keys | EnumMap |
| Object identity comparison | IdentityHashMap |
| GC-sensitive cache | WeakHashMap |
| No duplicates | HashSet |
| Ordered unique elements | LinkedHashSet |
| Sorted unique elements | TreeSet |
| FIFO queue | ArrayDeque |
| Priority processing | PriorityQueue |

---

# 8. Collections Cheat Sheet

## List

```text
ArrayList
```

- Fast random access
- Dynamic array

---

```text
LinkedList
```

- Fast insert/delete at ends
- Doubly linked list

---

## Set

```text
HashSet
```

- Fast lookup
- No ordering

---

```text
LinkedHashSet
```

- Insertion order maintained

---

```text
TreeSet
```

- Sorted
- Red-Black Tree

---

## Map

```text
HashMap
```

General-purpose key-value store.

---

```text
LinkedHashMap
```

Insertion/access order.

---

```text
TreeMap
```

Sorted keys.

---

```text
ConcurrentHashMap
```

Thread-safe concurrent access.

---

# 9. Spring Boot Usage

`LinkedHashMap`

- Ordered JSON responses
- LRU caches

`ConcurrentHashMap`

- In-memory caches
- Shared application state

`EnumMap`

- State machine mappings
- Configuration by enum

`List.of()`

- Immutable configuration
- Constant data

---

# 10. Best Practices

✅ Prefer interfaces:

```java
Map<K,V> map =
    new HashMap<>();
```

---

✅ Use immutable collections when modification is unnecessary.

---

✅ Use `EnumMap` when keys are enums.

---

✅ Use `ConcurrentHashMap` instead of `Hashtable`.

---

# 11. Common Mistakes

❌ Expecting `HashMap` to preserve order.

---

❌ Using `IdentityHashMap` expecting `equals()` behavior.

---

❌ Assuming `WeakHashMap` retains entries forever.

---

❌ Using mutable objects as keys.

---

❌ Using `LinkedList` as a stack instead of `ArrayDeque`.

---

# 12. Interview Questions

### Difference between HashMap and LinkedHashMap?

---

### Difference between HashMap and TreeMap?

---

### Difference between HashMap and WeakHashMap?

---

### Difference between HashMap and IdentityHashMap?

---

### When would you use EnumMap?

---

### What is an LRU Cache?

---

### How does LinkedHashMap implement an LRU cache?

---

### Difference between List.of() and Arrays.asList()?

**Hint:**
- `List.of()` is immutable.
- `Arrays.asList()` returns a fixed-size list backed by the array.

---

### Difference between immutable and unmodifiable collections?

---

### Which collection would you use for:

- Fast lookup?
- Sorted data?
- Thread-safe access?
- Priority scheduling?
- FIFO processing?
- LRU caching?

---

# 13. Exercises

1. Implement an LRU cache using LinkedHashMap.
2. Compare HashMap and TreeMap.
3. Use EnumMap with an enum.
4. Demonstrate IdentityHashMap behavior.
5. Create immutable collections using `List.of()`.

---

# 14. Final Collections Revision Sheet

## Lists

- ArrayList
- LinkedList

## Sets

- HashSet
- LinkedHashSet
- TreeSet

## Maps

- HashMap
- LinkedHashMap
- TreeMap
- ConcurrentHashMap
- WeakHashMap
- IdentityHashMap
- EnumMap

## Queues

- PriorityQueue
- ArrayDeque

## Utilities

- Collections
- Comparator
- Comparable

## Java 9+

- List.of()
- Set.of()
- Map.of()

## Interview Focus

- HashMap Internals
- Comparable vs Comparator
- ConcurrentHashMap
- PriorityQueue
- LinkedHashMap
- LRU Cache
- Collection Selection