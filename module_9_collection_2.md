# Module 9 - Collections (Part 2)

# ArrayList Deep Dive

> **Goal:** Master ArrayList from interview and production perspectives by understanding its internal implementation, resizing algorithm, memory layout, time complexities, fail-fast behavior, and best practices.

---

# Table of Contents

1. What is ArrayList?
2. Why ArrayList?
3. Internal Data Structure
4. Constructors
5. Capacity vs Size
6. Internal Working
7. Resizing Algorithm
8. Important Methods
9. Time Complexity
10. Memory Representation
11. RandomAccess Interface
12. Fail-Fast Iterator
13. ArrayList vs Array
14. ArrayList vs LinkedList
15. Spring Boot Usage
16. Best Practices
17. Common Mistakes
18. Interview Questions
19. Exercises
20. Revision Sheet

---

# 1. What is ArrayList?

`ArrayList` is a **resizable array implementation** of the `List` interface.

Characteristics:

- Ordered
- Allows duplicates
- Index-based access
- Dynamic resizing
- Not synchronized

Declaration:

```java
public class ArrayList<E>
        extends AbstractList<E>
        implements List<E>,
                   RandomAccess,
                   Cloneable,
                   Serializable
```

---

# Why Do We Need ArrayList?

Arrays have fixed size.

```java
int[] arr = new int[5];
```

After filling all 5 elements:

```text
Cannot add more elements.
```

ArrayList grows automatically.

```java
List<Integer> numbers = new ArrayList<>();

numbers.add(1);
numbers.add(2);
numbers.add(3);
```

No manual resizing required.

---

# 2. Internal Data Structure

Internally ArrayList stores elements in an array.

Simplified JDK code:

```java
transient Object[] elementData;
```

Every ArrayList object owns one internal array.

---

# Memory Representation

```text
Stack

list
 │
 ▼

Heap

ArrayList
      │
      ▼

Object[]

+------+------+------+------+
| Obj1 | Obj2 | Obj3 | null |
+------+------+------+------+
```

Notice:

The array stores **references**, not actual objects.

---

# 3. Constructors

## Default Constructor

```java
List<String> list = new ArrayList<>();
```

Modern JDK delays allocation until the first element is added.

---

## Initial Capacity

```java
List<String> list = new ArrayList<>(100);
```

Useful when expected size is known.

Avoids repeated resizing.

---

## From Collection

```java
List<String> copy =
        new ArrayList<>(existingList);
```

Creates a shallow copy.

---

# 4. Capacity vs Size

This is one of the most common interview questions.

## Size

Number of elements currently stored.

```java
list.add("Java");
list.add("Spring");
```

```text
Size = 2
```

---

## Capacity

Total number of elements that can fit before resizing.

Suppose

```text
Capacity = 10
```

You can store 10 objects before growth occurs.

---

Example

```java
ArrayList<String> list =
        new ArrayList<>();
```

Initially

```text
Size = 0

Capacity = 0 (lazy allocation)
```

After first add()

```text
Size = 1

Capacity = 10
```

---

# 5. Internal Working of add()

Suppose:

```java
list.add("Java");
```

Simplified JDK logic:

```java
public boolean add(E e){

    ensureCapacity();

    elementData[size++] = e;

    return true;

}
```

Execution:

```text
Check capacity

↓

Enough space?

↓

Yes

↓

Store object

↓

Increase size
```

---

# What if Capacity is Full?

Current array:

```text
Capacity = 10
```

Adding 11th element.

Java creates a larger array.

Copies all elements.

Old array becomes eligible for GC.

---

# 6. Resizing Algorithm

Old Capacity

```text
10
```

New Capacity

```text
15
```

Formula used by modern JDK:

```text
newCapacity = oldCapacity + (oldCapacity >> 1)
```

Equivalent to:

```text
old × 1.5
```

Examples

| Old | New |
|------|------|
| 10 | 15 |
| 15 | 22 |
| 22 | 33 |
| 33 | 49 |

---

# Internal Grow Method

Simplified:

```java
private Object[] grow(){

    int newCapacity =
        oldCapacity + (oldCapacity >> 1);

    return Arrays.copyOf(
            elementData,
            newCapacity
    );

}
```

---

# Why Not Increase by One Every Time?

Suppose:

```text
10

↓

11

↓

12

↓

13
```

Every insertion would require copying.

Complexity becomes O(n²).

Growing by 1.5x minimizes copying.

---

# 7. Important Methods

## add()

```java
list.add("Java");
```

Time:

```text
O(1) amortized
```

Worst case:

```text
O(n)
```

when resizing occurs.

---

## get()

```java
list.get(2);
```

Time

```text
O(1)
```

Because arrays support direct indexing.

---

## set()

```java
list.set(2,"Spring");
```

Updates an element.

Time

```text
O(1)
```

---

## remove(index)

```java
list.remove(2);
```

Remaining elements shift left.

Time

```text
O(n)
```

---

## contains()

```java
list.contains("Java");
```

Linear search.

Time

```text
O(n)
```

---

## clear()

Removes all references.

Size becomes zero.

Objects become eligible for Garbage Collection if no other references exist.

---

# 8. ensureCapacity()

Preallocates memory.

```java
list.ensureCapacity(1000);
```

Useful when importing large datasets.

Avoids multiple reallocations.

---

# trimToSize()

Shrinks capacity to match current size.

```java
list.trimToSize();
```

Useful after removing many elements.

---

# 9. Time Complexity

| Operation | Complexity |
|------------|------------|
| add(end) | O(1) amortized |
| add(index) | O(n) |
| remove(end) | O(1) |
| remove(index) | O(n) |
| get | O(1) |
| set | O(1) |
| contains | O(n) |
| indexOf | O(n) |
| clear | O(n) |

---

# 10. RandomAccess Interface

ArrayList implements

```java
RandomAccess
```

Marker interface.

Contains no methods.

Purpose:

Indicates fast index-based access.

Algorithms may optimize behavior based on this.

---

# 11. Fail-Fast Iterator

Example

```java
for(String s : list){

    list.add("Java");

}
```

Throws

```text
ConcurrentModificationException
```

Why?

Iterator detects structural modification.

Internally ArrayList maintains

```java
modCount
```

Iterator stores

```java
expectedModCount
```

If they differ:

```text
ConcurrentModificationException
```

is thrown.

We'll study this in depth in Part 8.

---

# 12. ArrayList vs Array

| Array | ArrayList |
|---------|-----------|
| Fixed size | Dynamic |
| Primitive support | Yes |
| Generics | No |
| Rich API | No |
| Automatic resizing | No |

---

# 13. ArrayList vs LinkedList

| ArrayList | LinkedList |
|------------|------------|
| Array | Doubly Linked List |
| Fast Random Access | Slow Random Access |
| Slow Middle Insert | Fast Middle Insert (if node known) |
| Better Cache Locality | Higher Memory Usage |
| Most commonly used | Less common |

We'll study LinkedList next.

---

# 14. Spring Boot Connection

Examples

Repository

```java
List<User> users =
        repository.findAll();
```

Controller

```java
@GetMapping

public List<UserDto> getUsers(){

}
```

DTO Conversion

```java
List<UserDto> dtos =
        new ArrayList<>();
```

ArrayList is the default List implementation in most Spring Boot applications.

---

# 15. Best Practices

✅ Program to `List`

```java
List<String> list =
        new ArrayList<>();
```

---

✅ Specify initial capacity when size is known.

---

✅ Prefer ArrayList over LinkedList unless frequent insertions/removals in the middle are required.

---

✅ Use enhanced for-loop or Iterator for traversal.

---

# 16. Common Mistakes

❌ Confusing size with capacity.

---

❌ Removing elements while iterating using a for-each loop.

---

❌ Assuming remove() is O(1).

---

❌ Using LinkedList everywhere because insertions are "faster" without considering traversal cost.

---

# 17. Interview Questions

### Why is ArrayList faster than LinkedList for get()?

---

### Explain internal working of add().

---

### Why is add() amortized O(1)?

---

### Difference between size and capacity?

---

### What happens when ArrayList becomes full?

---

### Explain resizing algorithm.

---

### Why does ArrayList implement RandomAccess?

---

### What is ConcurrentModificationException?

---

### Why is ArrayList not synchronized?

---

### When should ensureCapacity() be used?

---

# 18. Exercises

1. Draw ArrayList memory representation.
2. Explain resizing using an example.
3. Compare ArrayList and arrays.
4. Compare ArrayList and LinkedList.
5. Demonstrate ConcurrentModificationException.

---

# 19. Revision Sheet

## Internal Structure

- Object[]
- Dynamic Array
- References

## Important Concepts

- Capacity
- Size
- Growth
- Resizing
- Array Copy

## Methods

- add()
- remove()
- get()
- set()
- contains()
- clear()
- ensureCapacity()
- trimToSize()

## Internal Features

- RandomAccess
- modCount
- Fail-Fast Iterator

## Spring Boot Usage

- Repository Results
- DTO Lists
- REST Responses

## Interview Focus

- Resizing Algorithm
- Capacity vs Size
- Amortized Complexity
- RandomAccess
- ConcurrentModificationException