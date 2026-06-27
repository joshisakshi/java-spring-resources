# Module 9 - Collections (Part 5.3)

# HashMap Deep Dive - Part 3 (Advanced Internals)

> Goal: Master HashMap internals from the OpenJDK implementation including treeification, fail-fast iterators, source code walkthrough, custom keys, and advanced interview questions.

---

# Table of Contents

1. Review
2. Java 8 Improvements
3. Treeification
4. Why Bucket Size 8?
5. Why Capacity 64?
6. Untreeification
7. put() Source Walkthrough
8. get() Source Walkthrough
9. remove() Internal Working
10. modCount
11. Fail-Fast Iterator
12. ConcurrentModificationException
13. Custom Keys
14. hashCode() Contract
15. equals() Contract
16. Best Practices
17. Spring Boot Usage
18. Common Mistakes
19. Interview Questions
20. Revision Sheet

---

# 1. Quick Review

HashMap internally consists of:

```text
Bucket Array

↓

Node

↓

Linked List

↓

Red-Black Tree (Java 8+)
```

Average Complexity

```text
put()

O(1)

get()

O(1)
```

---

# 2. Java 8 Improvement

Before Java 8

Collisions were stored only as linked lists.

```text
Bucket

↓

Node

↓

Node

↓

Node

↓

Node
```

Worst-case lookup

```text
O(n)
```

Java 8 introduced:

```text
Red-Black Tree
```

Now worst-case becomes

```text
O(log n)
```

---

# 3. Treeification

Suppose bucket 5 contains many nodes.

```text
Bucket 5

↓

A

↓

B

↓

C

↓

D

↓

E

↓

F

↓

G

↓

H
```

Once enough nodes accumulate, Java converts the linked list into a Red-Black Tree.

```text
        D
      /   \
     B     F
    / \   / \
   A  C  E  G
             \
              H
```

This process is called **Treeification**.

---

# 4. Why Bucket Size = 8?

Interview Question ⭐⭐⭐⭐⭐

Treeification occurs only when:

```text
Bucket Size >= 8
```

Why 8?

Java engineers benchmarked different thresholds.

Smaller values:

- Tree creation overhead
- Extra memory
- Slower inserts

Larger values:

- Long linked lists
- Slow lookups

The value **8** provided the best trade-off.

---

# 5. Why Capacity Must Be At Least 64?

Another favorite interview question.

Treeification **does not happen** unless:

```text
Capacity >= 64
```

Example:

```text
Capacity = 16

Bucket Size = 9
```

HashMap will **resize** instead of creating a tree.

Reason:

A resize often distributes entries into new buckets, reducing collisions naturally.

Creating a tree too early would waste memory.

---

# 6. Untreeification

Suppose a tree bucket shrinks after deletions.

```text
8 Nodes

↓

7

↓

6
```

When bucket size becomes **less than 6**, Java converts the tree back into a linked list.

This process is called:

```text
Untreeification
```

---

# 7. Simplified put() Source Code

```java
put(key, value)

↓

hash(key)

↓

Find Bucket

↓

Bucket Empty?

↓

Yes

↓

Insert Node

↓

Done
```

Otherwise:

```text
Collision

↓

Traverse Linked List

↓

Same Key?

↓

Replace Value

↓

Else Add New Node

↓

Check Bucket Size

↓

>= 8 ?

↓

Treeify
```

---

# 8. Simplified get() Source Code

```java
get(key)

↓

hash(key)

↓

Find Bucket

↓

First Node Match?

↓

Return Value

↓

Else Traverse

↓

Linked List

or

Red-Black Tree

↓

equals()

↓

Return Value
```

---

# 9. remove()

Example

```java
map.remove("Java");
```

Execution

```text
hash()

↓

Bucket

↓

Find Node

↓

Reconnect Remaining Nodes

↓

Decrease Size
```

If using a tree bucket, the node is removed while maintaining Red-Black Tree properties.

---

# 10. modCount

HashMap internally maintains:

```java
int modCount;
```

Every structural modification increments it.

Examples:

```java
put()

remove()

clear()
```

increase `modCount`.

Updating the value of an existing key does **not** count as a structural modification because the number of entries remains the same.

---

# 11. Fail-Fast Iterator

Example

```java
Map<Integer,String> map = new HashMap<>();

map.put(1,"A");

for(Integer key : map.keySet()){

    map.put(2,"B");

}
```

Result

```text
ConcurrentModificationException
```

---

# Why?

Iterator stores:

```text
expectedModCount
```

HashMap stores:

```text
modCount
```

During iteration

```text
expectedModCount

!=

modCount
```

Java immediately throws

```text
ConcurrentModificationException
```

to avoid unpredictable behavior.

---

# 12. How to Remove Safely?

Wrong

```java
for(String key : map.keySet()){

    map.remove(key);

}
```

Correct

```java
Iterator<String> iterator =
        map.keySet().iterator();

while(iterator.hasNext()){

    iterator.next();

    iterator.remove();

}
```

The iterator updates its internal state safely.

---

# 13. Custom Keys

Suppose

```java
class Employee{

    int id;

    String name;

}
```

Using

```java
Employee e1 =
        new Employee(1,"Alice");

Employee e2 =
        new Employee(1,"Alice");
```

Without overriding

```java
equals()

hashCode()
```

HashMap treats them as different keys because the default implementations compare object identity.

---

# Correct Implementation

```java
@Override
public boolean equals(Object obj){

    // compare fields

}

@Override
public int hashCode(){

    // generate hash

}
```

Always override both methods together.

---

# 14. hashCode() Contract

If

```java
a.equals(b)
```

is true,

then

```java
a.hashCode()

==

b.hashCode()
```

must also be true.

The reverse is **not** required.

Two different objects may legally have the same hash code.

---

# 15. equals() Contract

Must be

- Reflexive
- Symmetric
- Transitive
- Consistent

And

```java
x.equals(null)
```

must always return

```text
false
```

---

# 16. Spring Boot Usage

HashMap appears everywhere.

Examples:

Caching

```java
Map<Long,User> cache;
```

JWT Claims

```java
Map<String,Object> claims;
```

HTTP Headers

```java
Map<String,String>
```

Configuration

```java
Map<String,String>
```

JSON serialization/deserialization libraries also frequently use maps internally.

---

# 17. Best Practices

✅ Override both `equals()` and `hashCode()`.

---

✅ Prefer immutable keys (`String`, `UUID`, `Integer`, etc.).

---

✅ Provide initial capacity when the approximate size is known.

---

✅ Program against the `Map` interface.

```java
Map<K,V> map = new HashMap<>();
```

---

# 18. Common Mistakes

❌ Assuming collisions are rare enough to ignore.

---

❌ Forgetting to override `hashCode()`.

---

❌ Using mutable objects as keys.

---

❌ Assuming iteration order is predictable.

---

❌ Modifying the map during iteration.

---

# 19. Interview Questions

### Explain HashMap internals.

---

### Why is average lookup O(1)?

---

### What is a collision?

---

### What is Separate Chaining?

---

### Why does Java convert linked lists into trees?

---

### Why is treeification threshold 8?

---

### Why is minimum capacity 64?

---

### What is untreeification?

---

### Explain `put()` internally.

---

### Explain `get()` internally.

---

### What is `modCount`?

---

### Why does ConcurrentModificationException occur?

---

### Difference between `hashCode()` and `equals()`?

---

### What happens if only `equals()` is overridden?

---

### What happens if only `hashCode()` is overridden?

---

### Can two unequal objects have the same hash code?

**Answer:** Yes.

---

### Can two equal objects have different hash codes?

**Answer:** No.

---

### Why are immutable keys recommended?

---

### Does updating an existing value increase the map size?

**Answer:** No.

---

# 20. Revision Sheet

## Core Concepts

- Bucket
- Node
- Collision
- Treeification
- Untreeification

## Important Numbers

- Default Capacity = 16
- Load Factor = 0.75
- Treeify Threshold = 8
- Untreeify Threshold = 6
- Minimum Capacity for Treeify = 64

## Internal Fields

- table
- Node
- modCount
- threshold

## Interview Focus

- put() Flow
- get() Flow
- Collision Handling
- Red-Black Trees
- Fail-Fast Iterator
- hashCode() Contract
- equals() Contract