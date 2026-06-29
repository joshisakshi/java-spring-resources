# Module 4 — Spring Data JPA & Hibernate (Response 2/4)

> **Interview Frequency:** ⭐⭐⭐⭐⭐ (Must Know)
>
> **Topics:** EntityManager • First Level Cache • Dirty Checking • Flush • Commit • Transaction Flow

---

# Goal

By the end of this chapter, you should understand:

- EntityManager
- First Level Cache
- Dirty Checking
- Flush vs Commit
- How @Transactional works with JPA
- Internal execution flow

These topics separate someone who **uses JPA** from someone who **understands JPA**.

---

# 1. What is EntityManager?

## Interview Definition

> EntityManager is the core JPA interface responsible for managing the lifecycle of entities and interacting with the Persistence Context.

Think of it as the **manager of all database operations**.

Almost everything Hibernate does goes through the EntityManager.

---

# Responsibilities of EntityManager

- Persist entities
- Find entities
- Update entities
- Remove entities
- Manage Persistence Context
- Execute JPQL queries
- Manage transactions (in plain JPA)

---

## Common Methods

```java
persist(entity);

find(Product.class, 1L);

remove(entity);

merge(entity);

flush();

clear();
```

Interview Tip:

You rarely use `EntityManager` directly in Spring Boot because `JpaRepository` uses it internally.

---

# Internal Architecture

```
Application

↓

JpaRepository

↓

EntityManager

↓

Persistence Context

↓

Hibernate

↓

Database
```

When you call:

```java
repository.save(product);
```

Eventually,

Hibernate uses the EntityManager internally.

---

# 2. First Level Cache ⭐⭐⭐⭐⭐

One of the most frequently asked interview questions.

Every Persistence Context has a cache.

This cache is called the **First Level Cache**.

```
Persistence Context

↓

First Level Cache
```

---

# Why do we need it?

Suppose:

```java
repository.findById(1);
```

Immediately after:

```java
repository.findById(1);
```

Should Hibernate hit the database twice?

No.

It already has the object.

---

# Internal Flow

First call

```
find(1)

↓

Cache Miss

↓

Database

↓

Store in Cache

↓

Return Object
```

Second call

```
find(1)

↓

Cache Hit

↓

Return Object

(No SQL)
```

Much faster.

---

# Interview Question

### Is First Level Cache enabled by default?

✅ Yes.

Always.

No configuration required.

---

# Second Level Cache

Interview Note:

You only need to know the difference.

| First Level Cache | Second Level Cache |
|-------------------|-------------------|
| Default | Optional |
| Per Persistence Context | Shared across sessions |
| Mandatory | Needs configuration |

For SDE-1/SDE-2, this is usually enough.

---

# 3. Dirty Checking ⭐⭐⭐⭐⭐

This is one of Hibernate's best features.

---

## Problem Without Dirty Checking

Imagine:

```java
Product p = repository.findById(1);

p.setPrice(80000);
```

Without Dirty Checking, you'd need:

```java
repository.update(p);
```

every time.

That would be tedious.

---

# Hibernate's Solution

Hibernate automatically detects changes.

Example

```java
@Transactional

public void updatePrice(){

    Product p = repository.findById(1).get();

    p.setPrice(80000);

}
```

No:

```java
repository.save(p);
```

Yet the database is updated.

Why?

Dirty Checking.

---

# Internal Working

Step 1

```
Load Entity

↓

Persistence Context
```

Hibernate stores:

```
Original State

Price = 65000
```

---

Step 2

Application changes object.

```
Price = 80000
```

---

Step 3

Transaction ends.

Hibernate compares:

```
Original

↓

65000

Current

↓

80000
```

Difference found.

Generate SQL.

```
UPDATE products

SET price = 80000

WHERE id = 1;
```

This comparison process is called **Dirty Checking**.

---

# Dirty Checking Diagram

```
Database

↓

Persistence Context

↓

Original Snapshot

↓

Application Changes Entity

↓

Hibernate Compares Snapshot

↓

Difference?

↓

YES

↓

Generate UPDATE SQL
```

This diagram alone answers many Hibernate interview questions.

---

# 4. Flush ⭐⭐⭐⭐⭐

One of the most misunderstood topics.

---

## What is Flush?

Flush means:

> Synchronize the Persistence Context with the database.

Important:

**Flush does NOT mean Commit.**

---

# Example

```java
@Transactional

public void update(){

    Product p = repository.findById(1).get();

    p.setPrice(90000);

}
```

Before transaction ends:

```
Persistence Context

↓

Updated
```

Database

```
Old Value
```

When Flush occurs:

```
UPDATE SQL

↓

Database Updated
```

Transaction may still be open.

---

# Flush vs Commit

| Flush | Commit |
|--------|---------|
| Sends SQL | Permanently saves transaction |
| Transaction still active | Transaction ends |
| Can happen multiple times | Happens once |

Interviewers love this distinction.

---

# When does Hibernate Flush?

Usually:

- Before Commit
- Before JPQL queries (if needed)
- Manual `flush()`

You don't need to memorize every flush mode for interviews.

---

# 5. Transaction Flow

Suppose

```java
@Transactional

public void updatePrice(){

    Product p = repository.findById(1).get();

    p.setPrice(90000);

}
```

Internal execution:

```
Transaction Starts

↓

Entity Loaded

↓

Persistence Context

↓

Snapshot Created

↓

Application Modifies Entity

↓

Dirty Checking

↓

Flush

↓

UPDATE SQL

↓

Commit

↓

Transaction Ends
```

This flow is extremely important.

---

# Why use @Transactional?

Without it,

Hibernate may not have a managed Persistence Context throughout the operation.

With it,

- One transaction
- One Persistence Context
- Dirty Checking works correctly
- Automatic flush at commit

We'll cover transaction propagation and isolation in Module 7.

---

# Production Example

Updating a user's email:

```java
@Transactional

public void updateEmail(Long id, String email){

    User user = repository.findById(id).orElseThrow();

    user.setEmail(email);

}
```

Notice:

No explicit:

```java
repository.save(user);
```

Dirty Checking handles the update.

---

# Common Interview Questions

### What is EntityManager?

The core JPA interface responsible for managing entities and the Persistence Context.

---

### What is First Level Cache?

An in-memory cache associated with the Persistence Context that avoids repeated database queries for the same entity.

---

### What is Dirty Checking?

Hibernate's mechanism for automatically detecting changes in managed entities and generating the required SQL updates.

---

### Difference between Flush and Commit?

**Flush**

- Synchronizes SQL with the database.
- Transaction is still active.

**Commit**

- Permanently commits the transaction.
- Ends the transaction.

---

### Does save() always execute UPDATE?

No.

If the entity is already managed inside a transaction, Dirty Checking may update it automatically without another `save()` call.

---

# Common Mistakes

❌ Thinking `flush()` commits the transaction.

❌ Calling `save()` after every setter inside a transaction.

❌ Assuming every `findById()` always hits the database.

❌ Not understanding why Dirty Checking requires managed entities.

---

# Revision Sheet

```
JpaRepository

↓

EntityManager

↓

Persistence Context

↓

First Level Cache

↓

Dirty Checking

↓

Flush

↓

Commit

↓

Database
```

Remember:

✅ EntityManager manages entities.

✅ Persistence Context stores managed entities.

✅ First Level Cache is always enabled.

✅ Dirty Checking automatically generates UPDATE statements.

✅ Flush ≠ Commit.

---

# Next Response

We'll cover another interview favorite:

- Relationships (`@OneToOne`, `@OneToMany`, `@ManyToOne`, `@ManyToMany`)
- Fetch Types
- Lazy vs Eager ⭐⭐⭐⭐⭐
- Cascade Types
- N+1 Query Problem ⭐⭐⭐⭐⭐

These topics are among the most common Hibernate questions in product-based company interviews.