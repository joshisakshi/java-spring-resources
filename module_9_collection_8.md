# Module 9 - Collections (Part 8)

# Queue, PriorityQueue & Deque Deep Dive

> **Goal:** Master Queue implementations in Java, understand PriorityQueue (Heap), Deque, ArrayDeque, their internal workings, time complexities, interview questions, and real-world Spring Boot usage.

---

# Table of Contents

1. Why Queues?
2. Queue Interface
3. Queue Hierarchy
4. Queue Methods
5. LinkedList as Queue
6. PriorityQueue
7. Internal Working of PriorityQueue
8. Min Heap vs Max Heap
9. Time Complexity
10. Deque Interface
11. ArrayDeque
12. Stack vs Queue vs Deque
13. Internal Working of ArrayDeque
14. Spring Boot Usage
15. Best Practices
16. Common Mistakes
17. Interview Questions
18. Exercises
19. Revision Sheet

---

# 1. Why Queues?

Many real-world systems process requests in the order they arrive.

Examples:

- Printer Queue
- CPU Scheduling
- Ticket Booking
- Order Processing
- Message Queues (Kafka, RabbitMQ)
- Web Server Request Queue

Example

```text
Request 1

↓

Request 2

↓

Request 3

↓

Processed in same order
```

This follows:

```text
FIFO

First In First Out
```

---

# 2. Queue Interface

Package

```java
java.util.Queue
```

Queue extends

```text
Collection

↓

Queue
```

It represents a collection where elements are generally processed in FIFO order.

---

# Queue Hierarchy

```text
Collection

↓

Queue

├── LinkedList

├── PriorityQueue

└── Deque

      ├── ArrayDeque

      └── LinkedList
```

---

# 3. Queue Methods

| Method | Description |
|---------|-------------|
| add() | Insert element |
| offer() | Insert safely |
| remove() | Remove head |
| poll() | Remove safely |
| element() | View head |
| peek() | View safely |

---

## add()

```java
Queue<Integer> queue =
        new LinkedList<>();

queue.add(10);
queue.add(20);
queue.add(30);
```

Queue

```text
10

20

30
```

---

## offer()

```java
queue.offer(40);
```

Difference

```text
add()

↓

Throws Exception

if insertion fails
```

```text
offer()

↓

Returns false

if insertion fails
```

Preferred for bounded queues.

---

## remove()

```java
queue.remove();
```

Removes

```text
10
```

Queue becomes

```text
20

30
```

If queue is empty

```text
NoSuchElementException
```

---

## poll()

```java
queue.poll();
```

Removes head.

If queue is empty

```text
null
```

instead of throwing an exception.

---

## element()

```java
queue.element();
```

Returns

```text
Head Element
```

Does not remove it.

Throws exception if queue is empty.

---

## peek()

```java
queue.peek();
```

Returns head.

Returns

```text
null
```

if empty.

---

# remove() vs poll()

| remove() | poll() |
|-----------|---------|
| Removes head | Removes head |
| Throws exception if empty | Returns null |
| Less safe | Preferred |

---

# element() vs peek()

| element() | peek() |
|------------|---------|
| Returns head | Returns head |
| Throws exception | Returns null |
| Less safe | Preferred |

---

# 4. LinkedList as Queue

LinkedList implements

```text
List

Queue

Deque
```

Example

```java
Queue<String> queue =
        new LinkedList<>();

queue.offer("A");
queue.offer("B");
queue.offer("C");

System.out.println(queue.poll());
```

Output

```text
A
```

---

# Internal Structure

```text
Head

↓

A

⇄

B

⇄

C

↓

Tail
```

Insertion at tail

Removal from head

Both are

```text
O(1)
```

---

# 5. PriorityQueue

Unlike Queue,

PriorityQueue does NOT follow insertion order.

It follows

```text
Priority
```

Default

```java
PriorityQueue<Integer> pq =
        new PriorityQueue<>();
```

Example

```java
pq.add(30);

pq.add(10);

pq.add(20);
```

Removing

```java
System.out.println(pq.poll());
```

Output

```text
10
```

Even though

```text
30

was inserted first.
```

---

# Why?

PriorityQueue internally uses

```text
Binary Heap
```

Not LinkedList.
