# Module 4 — Spring Data JPA & Hibernate (Response 3/4)

> **Interview Frequency:** ⭐⭐⭐⭐⭐ (Highest ROI)
>
> **Topics:** Entity Relationships • Fetch Types • Cascade Types • Lazy vs Eager • N+1 Problem

---

# Goal

By the end of this chapter you should understand:

- Entity Relationships
- FetchType
- CascadeType
- Lazy vs Eager Loading
- N+1 Query Problem

These topics are among the **Top 10 Hibernate interview questions**.

---

# 1. Entity Relationships

In real-world applications, tables are connected.

Example:

```
Customer

↓

Orders

↓

Order Items

↓

Products
```

Similarly, Java objects also need relationships.

Hibernate provides four relationship annotations.

---

# Relationship Overview

| Relationship | Example |
|--------------|---------|
| @OneToOne | User ↔ Passport |
| @OneToMany | Customer → Orders |
| @ManyToOne | Order → Customer |
| @ManyToMany | Student ↔ Course |

Don't memorize definitions.

Visualize them.

---

# @OneToOne

Example:

```
Person

↓

Passport
```

Each person has one passport.

Each passport belongs to one person.

```java
@Entity
class Person {

    @OneToOne
    private Passport passport;

}
```

Database

```
Person

passport_id

↓

Passport
```

---

# @OneToMany

Example

```
Customer

↓

Many Orders
```

```
Customer

↓

Order 1

Order 2

Order 3
```

```java
@OneToMany(mappedBy="customer")

private List<Order> orders;
```

Think:

One parent.

Many children.

---

# @ManyToOne ⭐⭐⭐⭐⭐

This is the relationship you'll use the most.

Many Orders

↓

One Customer

```java
@ManyToOne

private Customer customer;
```

Database

```
Orders Table

customer_id

↓

Customers Table
```

---

# @ManyToMany

Example

```
Students

↓

Courses
```

One student

↓

Many courses

One course

↓

Many students

Requires a join table.

```
student_course
```

Interview Tip:

Many-to-Many is less common in production.

Most companies replace it with an intermediate entity.

Example

```
Enrollment
```

instead of direct Many-to-Many.

---

# Which Relationship is Most Common?

Production ranking:

```
★★★★★ @ManyToOne

★★★★☆ @OneToMany

★★★☆☆ @OneToOne

★★☆☆☆ @ManyToMany
```

---

# FetchType ⭐⭐⭐⭐⭐

Another favorite interview topic.

Question:

Suppose you load an Order.

Should Hibernate also load Customer?

Should it load OrderItems?

Should it load Products?

Two options exist.

---

# EAGER Loading

Load everything immediately.

```
Database

↓

Order

↓

Customer

↓

Address

↓

Products

↓

Payments
```

Everything is loaded.

Even if you don't need it.

---

# LAZY Loading

Load only the requested entity.

```
Order

↓

Loaded

Customer

↓

Not Loaded Yet

Products

↓

Not Loaded Yet
```

Only when accessed:

```java
order.getCustomer();
```

Hibernate performs another SQL query.

---

# Lazy vs Eager

| Lazy | Eager |
|------|-------|
| Load when needed | Load immediately |
| Better Performance | Can be slower |
| Less Memory | More Memory |
| Preferred | Use carefully |

Interview Answer:

> **Prefer LAZY loading unless you have a strong reason to use EAGER.**

---

# Default Fetch Types ⭐⭐⭐⭐⭐

Interviewers ask this surprisingly often.

| Relationship | Default |
|--------------|---------|
| @ManyToOne | EAGER |
| @OneToOne | EAGER |
| @OneToMany | LAZY |
| @ManyToMany | LAZY |

Many developers explicitly set:

```java
fetch = FetchType.LAZY
```

even for `@ManyToOne` to avoid unnecessary queries.

---

# Internal Working of Lazy Loading

Suppose

```java
Order order = repository.findById(1).get();
```

Hibernate loads

```
Order
```

But Customer becomes a **proxy object**.

```
Order

↓

Customer Proxy
```

When

```java
order.getCustomer();
```

Hibernate executes another SQL query.

This behavior is implemented using **Hibernate Proxies**, which we'll revisit when discussing AOP and proxies.

---

# Cascade Types ⭐⭐⭐⭐☆

Cascade determines what happens to child entities when an operation is performed on the parent.

Example

Customer

↓

Orders

Delete Customer?

↓

Delete Orders too?

Cascade decides.

---

# Common Cascade Types

| Cascade | Meaning |
|----------|---------|
| PERSIST | Save child automatically |
| MERGE | Update child |
| REMOVE | Delete child |
| ALL | Perform all operations |

---

# Example

```java
@OneToMany(

cascade = CascadeType.ALL)

private List<Order> orders;
```

Now

```java
entityManager.persist(customer);
```

Automatically saves:

```
Customer

↓

Order 1

↓

Order 2

↓

Order 3
```

without calling `save()` for each order.

---

# Should You Always Use Cascade.ALL?

No.

Interview Tip:

Use only the cascades you actually need.

Blindly using `CascadeType.ALL` can accidentally delete related data.

---

# N+1 Problem ⭐⭐⭐⭐⭐

One of the **most important Hibernate interview questions**.

---

# What is the N+1 Problem?

Suppose there are:

```
100 Orders
```

Each Order has one Customer.

You execute

```java
findAllOrders();
```

Hibernate performs:

```
1 Query

↓

Load Orders
```

Then,

for every order

```
Load Customer
```

Result

```
1

+

100

=

101 Queries
```

This is called the **N+1 Problem**.

---

# Visualization

```
SELECT * FROM orders;

↓

100 Orders

↓

SELECT customer WHERE id=1

↓

SELECT customer WHERE id=2

↓

SELECT customer WHERE id=3

...

↓

101 Queries
```

Huge performance issue.

---

# Why is it Bad?

Imagine

```
5000 Orders
```

Now

```
5001 SQL Queries
```

Network overhead becomes enormous.

The database spends more time processing queries than returning data.

---

# How to Solve It?

The interview expectation is that you know the common solutions.

### 1. JOIN FETCH (Most Common)

```java
SELECT o

FROM Order o

JOIN FETCH o.customer
```

Loads everything in a single query.

---

### 2. EntityGraph

Allows fetching related entities without changing entity mappings.

Useful for different fetch requirements in different use cases.

---

### 3. DTO Projections

Instead of loading full entities,

fetch only required fields.

Example

```
Order ID

Customer Name

Amount
```

This is often the best option for read-heavy APIs.

---

# Interview Tip

Don't answer:

> "Use EAGER to fix N+1."

That often makes performance worse.

Instead say:

> "Keep relationships LAZY by default and fetch required associations explicitly using JOIN FETCH, EntityGraph, or DTO projections."

---

# Complete Relationship Flow

```
Repository

↓

Hibernate

↓

Order Entity

↓

Customer Proxy

↓

Application accesses Customer

↓

Additional SQL (Lazy)

↓

Customer Loaded
```

---

# Common Interview Questions

### Which relationship is most common?

`@ManyToOne`

---

### Difference between LAZY and EAGER?

Lazy loads data only when accessed.

Eager loads immediately.

---

### Which FetchType should you prefer?

LAZY.

---

### What is Cascade?

It propagates persistence operations from parent to child entities.

---

### What is the N+1 Problem?

One query loads parent entities.

N additional queries load child entities.

Result:

```
1 + N SQL Queries
```

---

### How do you solve N+1?

- JOIN FETCH
- EntityGraph
- DTO Projection

---

# Common Mistakes

❌ Using `FetchType.EAGER` everywhere.

❌ Using `CascadeType.ALL` blindly.

❌ Returning entities with many lazy relationships directly from REST controllers.

❌ Ignoring SQL logs and not noticing N+1 queries.

---

# Revision Sheet

## Relationships

```
@OneToOne

@OneToMany

@ManyToOne ⭐⭐⭐⭐⭐

@ManyToMany
```

---

## Fetch Types

```
LAZY

↓

Load on Demand

Preferred ✅
```

```
EAGER

↓

Load Immediately

Use Carefully ⚠️
```

---

## Cascade

```
Parent

↓

PERSIST

MERGE

REMOVE

ALL

↓

Child
```

---

## N+1

```
1 Query

+

N Queries

=

Performance Problem
```

Solve using:

- JOIN FETCH ✅
- EntityGraph ✅
- DTO Projection ✅

---

# Next Response (Final Module 4)

We'll complete JPA with:

- `JpaRepository`
- Derived Query Methods
- JPQL vs Native SQL
- Pagination
- Sorting
- `@Transactional`
- Transaction Propagation (Interview Level)
- Production Best Practices
- Module 4 Cheat Sheet
- Top 30 Hibernate Interview Questions

This will complete everything expected from **Spring Data JPA & Hibernate** for SDE-1/SDE-2 backend interviews.