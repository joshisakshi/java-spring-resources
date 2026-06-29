# Module 4 — Spring Data JPA & Hibernate (Response 1/4)

> **Interview Frequency:** ⭐⭐⭐⭐⭐ (Highest Priority)
>
> **Master these topics:** ORM • Hibernate • Entity • Persistence Context
>
> **Revision Time:** ~60 mins

---

# Goal

By the end of this chapter you should understand:

- Why ORM exists
- JPA vs Hibernate
- Hibernate Architecture
- Entity
- Entity Lifecycle
- Persistence Context (Most Important)

This is one of the highest ROI topics for backend interviews.

---

# Why ORM?

Suppose Java directly communicated with MySQL.

Without ORM:

```java
Connection con = DriverManager.getConnection(...);

PreparedStatement ps = con.prepareStatement(
"INSERT INTO products VALUES (?, ?)");

ps.setString(1, "Laptop");

ps.executeUpdate();
```

Problems:

- Boilerplate code
- Manual mapping
- Error-prone
- Database-specific SQL everywhere

---

# ORM (Object Relational Mapping)

ORM maps:

```
Java Object

⬄

Database Row
```

Example

Java

```java
Product product =
new Product(1L,"Laptop",65000);
```

Database

| id | name | price |
|----|------|-------|
|1|Laptop|65000|

ORM converts between both automatically.

---

# What is JPA?

**Interview Definition**

JPA (Java Persistence API) is a **specification** that defines how Java objects should be persisted to relational databases.

Important:

❌ JPA is NOT an implementation.

Think of it as an interface or contract.

---

# What is Hibernate?

Hibernate is the **most popular implementation of JPA**.

```
JPA

↓

Specification

↓

Hibernate

↓

Implementation
```

Analogy:

```
List

↓

Interface

↓

ArrayList

↓

Implementation
```

Similarly,

```
JPA

↓

Hibernate
```

---

# Where does Spring Data JPA fit?

```
Application

↓

Spring Data JPA

↓

Hibernate

↓

JDBC

↓

Database
```

Responsibilities:

Spring Data JPA

- Repository abstraction
- Query generation

Hibernate

- ORM
- SQL generation
- Entity lifecycle
- Persistence Context

JDBC

- Database communication

---

# Hibernate Architecture

```
Application

↓

Repository

↓

EntityManager

↓

Persistence Context

↓

Hibernate

↓

JDBC

↓

Database
```

This diagram is worth memorizing.

---

# Entity

An Entity is a Java object mapped to a database table.

Example

```java
@Entity
@Table(name = "products")
public class Product {

    @Id
    private Long id;

    private String name;

    private double price;

}
```

Mapping

```
Product Class

↓

products Table
```

Each object represents one row.

---

# Common Entity Annotations

| Annotation | Purpose |
|------------|---------|
| @Entity | Marks persistent class |
| @Table | Database table name |
| @Id | Primary Key |
| @GeneratedValue | Auto-generate ID |
| @Column | Customize column |

Interview Tip:

You don't need `@Column` unless customizing behavior.

---

# Entity Lifecycle ⭐⭐⭐⭐⭐

One of the most asked Hibernate topics.

Every entity exists in one of four states.

```
Transient

↓

Persistent

↓

Detached

↓

Removed
```

Understanding these states explains many Hibernate behaviors.

---

# 1. Transient

Object exists only in JVM memory.

Not tracked by Hibernate.

```java
Product product =

new Product();
```

Current state:

```
Memory

✓ Exists

Database

✗ Doesn't Exist
```

---

# 2. Persistent

Entity is now managed by Hibernate.

Example

```java
entityManager.persist(product);
```

Now:

```
Memory

✓

Persistence Context

✓

Database

✓ (after flush/commit)
```

Hibernate starts tracking changes.

---

# 3. Detached

Suppose transaction finishes.

```
Persistent

↓

Session Closed

↓

Detached
```

Object still exists.

Hibernate stops tracking it.

Changes made now are **not automatically saved**.

---

# 4. Removed

```java
entityManager.remove(product);
```

Entity is marked for deletion.

Actual SQL usually executes during flush/commit.

---

# Entity Lifecycle Diagram

```
new Product()

↓

Transient

↓

persist()

↓

Persistent

↓

close()

↓

Detached

↓

remove()

↓

Removed
```

This diagram appears frequently in interviews.

---

# Persistence Context ⭐⭐⭐⭐⭐

This is the single most important Hibernate concept.

Interview Definition:

> Persistence Context is a cache that stores and manages entities during a transaction.

Another way to think about it:

It is Hibernate's **working memory**.

---

# Real Analogy

Imagine editing a Word document.

You type:

```
Hello
```

Has it been saved to disk immediately?

No.

It first lives in memory.

When you click Save,

it is written to disk.

Persistence Context works similarly.

```
Database

↓

Load Entity

↓

Persistence Context

↓

Modify Entity

↓

Commit

↓

Database Updated
```

---

# Why Persistence Context?

Without it:

Every getter/setter change would require SQL.

That would be extremely slow.

Instead,

Hibernate keeps entities in memory,

tracks changes,

and writes them later.

---

# Internal Working

Suppose

```java
Product p =

repository.findById(1L);
```

Execution

```
Database

↓

SELECT

↓

Persistence Context

↓

Product Object

↓

Application
```

Now

```java
p.setPrice(70000);
```

No SQL runs immediately.

Hibernate simply updates the in-memory object.

Only during flush/commit:

```
UPDATE products
SET price=70000
WHERE id=1
```

This optimization is the foundation for Dirty Checking (next chapter).

---

# Why is Persistence Context Important?

It provides:

- First Level Cache
- Dirty Checking
- Identity Management
- Automatic Updates
- Better Performance

Most advanced Hibernate features rely on it.

---

# Common Interview Questions

### JPA vs Hibernate?

| JPA | Hibernate |
|-----|-----------|
| Specification | Implementation |
| Defines API | Implements API |

---

### What is an Entity?

A Java object mapped to a database table.

---

### What are Entity States?

- Transient
- Persistent
- Detached
- Removed

---

### What is Persistence Context?

An in-memory cache managed by Hibernate that stores entities during a transaction and tracks their changes.

---

### Does changing a Persistent Entity immediately execute SQL?

No.

Changes remain inside the Persistence Context.

SQL is executed during **flush/commit**.

---

# Common Mistakes

❌ Thinking JPA is Hibernate.

❌ Assuming every setter executes SQL.

❌ Confusing Persistence Context with Database.

❌ Not understanding entity lifecycle.

---

# Revision Sheet

```
Application

↓

Repository

↓

EntityManager

↓

Persistence Context

↓

Hibernate

↓

JDBC

↓

Database
```

Entity States

```
Transient

↓

Persistent

↓

Detached

↓

Removed
```

Remember:

✅ Hibernate manages Entities.

✅ Persistence Context manages Hibernate.

✅ Database updates usually happen at Flush/Commit.

---

# Next Response

We'll cover the **most asked Hibernate internals**:

- EntityManager
- First Level Cache
- Dirty Checking ⭐⭐⭐⭐⭐
- Flush
- Commit
- Transaction Flow

These concepts are asked in almost every backend interview involving Spring Data JPA.