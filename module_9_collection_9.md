# Module 9 - Collections (Part 9)

# Collections Utility Class Deep Dive

> **Goal:** Master the `java.util.Collections` utility class, understand its commonly used methods, wrappers, immutable views, and interview use cases.

---

# Table of Contents

1. What is Collections?
2. Collections vs Collection
3. Sorting
4. Searching
5. Reversing & Shuffling
6. Min & Max
7. Frequency & Disjoint
8. Copy & Fill
9. Synchronized Wrappers
10. Unmodifiable Collections
11. Empty & Singleton Collections
12. Spring Boot Usage
13. Best Practices
14. Common Mistakes
15. Interview Questions
16. Exercises
17. Revision Sheet

---

# 1. What is Collections?

`Collections` is a **utility class** that provides static helper methods for working with collections.

Package:

```java
java.util.Collections
```

Example:

```java
Collections.sort(list);

Collections.reverse(list);

Collections.shuffle(list);
```

Unlike `Collection`, it cannot be instantiated.

---

# 2. Collections vs Collection

| Collection | Collections |
|------------|-------------|
| Interface | Utility Class |
| Represents data structure | Provides helper methods |
| Implemented by List, Set, Queue | Contains static methods |

Example:

```java
Collection<String> c;

Collections.sort(list);
```

---

# 3. Sorting

Natural ordering:

```java
Collections.sort(list);
```

Custom ordering:

```java
Collections.sort(list,
        Comparator.reverseOrder());
```

Time Complexity:

```text
O(n log n)
```

Internally:

```text
Collections.sort()

↓

List.sort()

↓

TimSort
```

---

# 4. Binary Search

```java
Collections.binarySearch(list, 25);
```

Requirement:

The list **must already be sorted**.

Example:

```java
List<Integer> list =
        Arrays.asList(10,20,30,40);

Collections.binarySearch(list,30);
```

Output:

```text
2
```

Time Complexity:

```text
O(log n)
```

---

# 5. Reverse

```java
Collections.reverse(list);
```

Before:

```text
10 20 30 40
```

After:

```text
40 30 20 10
```

---

# 6. Shuffle

Randomizes element order.

```java
Collections.shuffle(list);
```

Example:

Before

```text
A B C D
```

After

```text
C A D B
```

Useful for:

- Quiz apps
- Card games
- Random sampling

---

# 7. Swap

```java
Collections.swap(list,0,2);
```

Before

```text
A B C
```

After

```text
C B A
```

---

# 8. Rotate

Rotates elements.

```java
Collections.rotate(list,2);
```

Before

```text
1 2 3 4 5
```

After

```text
4 5 1 2 3
```

Useful for circular scheduling and buffer simulations.

---

# 9. Min & Max

Minimum

```java
Collections.min(list);
```

Maximum

```java
Collections.max(list);
```

Example

```java
List<Integer> list =
        Arrays.asList(3,7,2,10);

Collections.min(list);

Collections.max(list);
```

Output

```text
2

10
```

---

# 10. Frequency

Counts occurrences.

```java
Collections.frequency(list,"Java");
```

Example

```text
Java

Spring

Java

SQL
```

Result

```text
2
```

---

# 11. Disjoint

Checks whether two collections have **no common elements**.

```java
Collections.disjoint(list1,list2);
```

Returns:

```text
true
```

if there is no intersection.

---

# 12. Copy

Copies elements from one list to another.

```java
Collections.copy(destination,
                 source);
```

Important:

Destination list must already have enough elements.

Example

```java
List<String> source =
    Arrays.asList("A","B");

List<String> destination =
    new ArrayList<>(
        Arrays.asList("X","Y")
    );

Collections.copy(destination,source);
```

Result

```text
A

B
```

---

# 13. Fill

```java
Collections.fill(list,"Java");
```

Example

Before

```text
A B C
```

After

```text
Java Java Java
```

---

# 14. Empty Collections

Instead of

```java
new ArrayList<>();
```

you can use

```java
Collections.emptyList();

Collections.emptySet();

Collections.emptyMap();
```

Advantages:

- Immutable
- Reusable
- No unnecessary object creation

---

# 15. Singleton Collections

Exactly one element.

```java
Collections.singleton("Java");

Collections.singletonList("Java");

Collections.singletonMap("A",1);
```

Useful for returning small immutable results.

---

# 16. Synchronized Wrappers

```java
List<String> list =
Collections.synchronizedList(
    new ArrayList<>()
);
```

Other wrappers:

```java
Collections.synchronizedSet()

Collections.synchronizedMap()
```

Useful when a thread-safe wrapper is sufficient.

---

# 17. Unmodifiable Collections

Creates a read-only view.

```java
List<String> list =
Collections.unmodifiableList(
    original
);
```

Attempting:

```java
list.add("Java");
```

throws

```text
UnsupportedOperationException
```

Note:

The wrapper prevents modification through that reference, but if the original collection changes, the unmodifiable view reflects those changes.

---

# 18. Spring Boot Usage

Frequently used for:

Returning immutable API responses.

```java
return Collections.emptyList();
```

Default values.

Read-only configuration.

Sorting DTOs.

Random test data.

Caching immutable collections.

---

# 19. Best Practices

✅ Use `Collections.emptyList()` instead of returning `null`.

---

✅ Use `Collections.unmodifiableList()` for read-only views.

---

✅ Use `Collections.singletonList()` for single-value returns.

---

✅ Ensure lists are sorted before using `binarySearch()`.

---

# 20. Common Mistakes

❌ Calling `binarySearch()` on an unsorted list.

---

❌ Assuming `unmodifiableList()` creates a deep copy.

(It creates a read-only view.)

---

❌ Forgetting destination size in `Collections.copy()`.

---

❌ Returning `null` instead of an empty collection.

---

# 21. Interview Questions

### Difference between Collection and Collections?

---

### What algorithm does `Collections.sort()` use?

**Answer:** TimSort.

---

### Time complexity of `binarySearch()`?

**Answer:** O(log n)

---

### What happens if `binarySearch()` is used on an unsorted list?

**Answer:** Result is undefined.

---

### Difference between `emptyList()` and `new ArrayList<>()`?

---

### Difference between `singletonList()` and `Arrays.asList()`?

---

### What is an unmodifiable collection?

---

### Difference between synchronized and unmodifiable collections?

---

### Does `Collections.copy()` increase the destination list size?

**Answer:** No.

---

# 22. Exercises

1. Sort a list in descending order.
2. Shuffle a list.
3. Reverse a list.
4. Use `binarySearch()`.
5. Return `Collections.emptyList()` from a method.
6. Create an unmodifiable list.

---

# 23. Revision Sheet

## Utility Methods

- sort()
- reverse()
- shuffle()
- swap()
- rotate()

## Searching

- binarySearch()

## Statistics

- min()
- max()
- frequency()

## Utilities

- copy()
- fill()
- disjoint()

## Wrappers

- synchronizedList()
- synchronizedSet()
- synchronizedMap()

## Immutable Helpers

- emptyList()
- singletonList()
- unmodifiableList()

## Interview Focus

- Collection vs Collections
- TimSort
- Binary Search
- Immutable Views
- Thread-safe Wrappers