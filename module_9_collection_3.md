# Module 9 - Collections (Part 3)

# LinkedList Deep Dive

> **Goal:** Understand LinkedList internals, node structure, traversal, insertion/deletion, queue/deque operations, memory layout, and when to choose LinkedList over ArrayList.

---

# Table of Contents

1. What is LinkedList?
2. Why LinkedList?
3. Internal Data Structure
4. Constructors
5. Node Structure
6. Internal Working
7. Traversal Optimization
8. Important Methods
9. Time Complexity
10. Memory Representation
11. LinkedList as Queue & Deque
12. LinkedList vs ArrayList
13. Spring Boot Connection
14. Best Practices
15. Common Mistakes
16. Interview Questions
17. Exercises
18. Revision Sheet

---

# 1. What is LinkedList?

`LinkedList` is a **doubly linked list implementation** of both the **List** and **Deque** interfaces.

Declaration:

```java
public class LinkedList<E>
        extends AbstractSequentialList<E>
        implements List<E>,
                   Deque<E>,
                   Cloneable,
                   Serializable
```

Characteristics:

- Ordered
- Allows duplicates
- Dynamic size
- Implements Queue and Deque
- Not synchronized

---

# Why Do We Need LinkedList?

Arrays require shifting elements when inserting or deleting in the middle.

Example:

```text
Before

[10][20][30][40]

Insert 25

↓

[10][20][25][30][40]
```

Elements must shift.

LinkedList only changes node references.

---

# 2. Internal Data Structure

Unlike ArrayList, LinkedList does **not** use an array.

It consists of nodes connected by pointers.

```text
+------+     +------+     +------+
| 10   | --> | 20   | --> | 30   |
+------+     +------+     +------+
```

Java actually uses a **doubly linked list**.

```text
null
  ↑
+-----------------------------+
| prev | data | next          |
+-----------------------------+
      ↕
+-----------------------------+
| prev | data | next          |
+-----------------------------+
      ↕
+-----------------------------+
| prev | data | next          |
+-----------------------------+
                              ↓
                             null
```

Each node knows:

- Previous node
- Current data
- Next node

---

# 3. Internal Node Structure

Simplified JDK source:

```java
private static class Node<E> {

    E item;
    Node<E> next;
    Node<E> prev;

    Node(Node<E> prev,
         E element,
         Node<E> next) {

        this.item = element;
        this.next = next;
        this.prev = prev;
    }
}
```

Each node stores:

- Data
- Previous reference
- Next reference

---

# LinkedList Fields

Simplified:

```java
transient Node<E> first;

transient Node<E> last;

transient int size;
```

The LinkedList object only keeps references to:

- First node
- Last node
- Size

---

# 4. Memory Representation

Suppose:

```java
list.add("A");
list.add("B");
list.add("C");
```

Memory:

```text
Stack

list
 │
 ▼

Heap

LinkedList
 ├── first ───────┐
 ├── last ───────────────┐
 └── size = 3

          ▼

+----------------------+
| prev | A | next ---- |----+
+----------------------+    |
                             ▼
                     +----------------------+
                     | prev | B | next ---- |----+
                     +----------------------+    |
                                                  ▼
                                          +----------------------+
                                          | prev | C | null      |
                                          +----------------------+
```

---

# 5. Internal Working of add()

Suppose:

```java
list.add("Java");
```

Simplified:

```java
Node<E> newNode =
        new Node<>(last, e, null);

last.next = newNode;

last = newNode;

size++;
```

Only references change.

No array copying.

---

# Insertion at Beginning

```java
list.addFirst("Java");
```

Execution:

```text
newNode.next = first

first.prev = newNode

first = newNode
```

Time Complexity:

```text
O(1)
```

---

# Insertion in Middle

Need to locate the node first.

Finding node:

```text
O(n)
```

Updating references:

```text
O(1)
```

Overall:

```text
O(n)
```

---

# 6. Internal Working of get(index)

Interview Question

Many think LinkedList always starts from the head.

Actually, JDK optimizes traversal.

Simplified:

```java
if(index < size / 2){

    start from first;

}else{

    start from last;

}
```

Example:

List Size:

```text
100
```

Request:

```text
get(90)
```

Traversal starts from the **tail**, not the head.

This reduces traversal time.

---

# 7. Important Methods

## add()

```java
list.add("Spring");
```

---

## addFirst()

```java
list.addFirst("Java");
```

---

## addLast()

```java
list.addLast("Spring");
```

---

## remove()

```java
list.remove();
```

Removes first element.

---

## removeFirst()

```java
list.removeFirst();
```

---

## removeLast()

```java
list.removeLast();
```

---

## getFirst()

```java
list.getFirst();
```

---

## getLast()

```java
list.getLast();
```

---

## peek()

Returns first element without removing it.

---

## poll()

Returns and removes first element.

---

# 8. Time Complexity

| Operation | Complexity |
|------------|------------|
| addFirst() | O(1) |
| addLast() | O(1) |
| removeFirst() | O(1) |
| removeLast() | O(1) |
| get(index) | O(n) |
| contains() | O(n) |
| add(index) | O(n) |
| remove(index) | O(n) |

---

# Why Random Access is Slow

Unlike arrays:

```text
Index 5

↓

Need to traverse nodes.
```

Cannot directly calculate memory address.

Therefore:

```text
get(index)

↓

O(n)
```

---

# 9. LinkedList as Queue

Queue operations:

```java
Queue<Integer> queue =
        new LinkedList<>();

queue.offer(10);

queue.offer(20);

queue.poll();

queue.peek();
```

FIFO behavior.

---

# LinkedList as Deque

```java
Deque<Integer> deque =
        new LinkedList<>();

deque.addFirst(10);

deque.addLast(20);

deque.removeFirst();

deque.removeLast();
```

Supports insertion/removal from both ends.

---

# 10. LinkedList vs ArrayList

| Feature | ArrayList | LinkedList |
|----------|-----------|------------|
| Internal Structure | Array | Doubly Linked List |
| Random Access | O(1) | O(n) |
| Insert End | O(1) Amortized | O(1) |
| Insert Middle | O(n) | O(n) |
| Remove Middle | O(n) | O(n) |
| Memory Usage | Lower | Higher |
| Cache Locality | Better | Poor |
| Queue Operations | Less Efficient | Excellent |

---

# Why ArrayList Often Performs Better

Although LinkedList has O(1) insertion/deletion at a known node:

- Finding the node is O(n)
- Each node is a separate object
- Poor CPU cache locality
- Extra memory for pointers

In most real applications, **ArrayList is usually faster**.

---

# 11. Spring Boot Connection

LinkedList is commonly used for:

- Queue implementations
- BFS algorithms
- Task scheduling
- Message processing
- Deque operations

For most REST API responses, `ArrayList` is still preferred.

---

# 12. Best Practices

✅ Use LinkedList when frequent insertions/removals occur at the beginning or end.

✅ Use LinkedList when implementing Queue or Deque.

✅ Prefer ArrayList for random access.

---

# 13. Common Mistakes

❌ Assuming LinkedList is always faster.

❌ Using LinkedList for index-based access.

❌ Ignoring memory overhead.

❌ Choosing LinkedList only because insertion is O(1), while forgetting traversal cost.

---

# 14. Interview Questions

### How is LinkedList implemented internally?

---

### Why is LinkedList called a doubly linked list?

---

### Why is get(index) O(n)?

---

### How does JDK optimize traversal?

---

### Difference between addFirst() and add()?

---

### Why doesn't LinkedList implement RandomAccess?

---

### Why does LinkedList consume more memory?

---

### When should you choose LinkedList over ArrayList?

---

### Can LinkedList be used as a Queue?

---

### Can LinkedList be used as a Deque?

---

# 15. Exercises

1. Draw the internal node structure of LinkedList.
2. Explain how addFirst() works.
3. Explain how get(index) works.
4. Compare LinkedList and ArrayList.
5. Implement a Queue using LinkedList.

---

# 16. Revision Sheet

## Core Concepts

- Doubly Linked List
- Node
- first
- last
- prev
- next

## Methods

- addFirst()
- addLast()
- removeFirst()
- removeLast()
- peek()
- poll()

## Internal Features

- Node Traversal
- Tail Optimization
- No Random Access

## Spring Boot Usage

- Queue
- Deque
- BFS
- Task Processing

## Interview Focus

- Node Structure
- Traversal Optimization
- Memory Overhead
- Queue Implementation
- ArrayList vs LinkedList