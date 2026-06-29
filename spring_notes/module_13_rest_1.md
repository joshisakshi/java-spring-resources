# Module 3 — REST APIs (Response 1/3)

> **Interview Frequency:** ⭐⭐⭐⭐⭐ (Must Know)
>
> Companies: Google • Amazon • Walmart • Atlassian • Razorpay • PhonePe • Flipkart
>
> **Revision Time:** ~45 mins
>
> **Verdict:** Master this chapter. REST + Request Lifecycle is asked in almost every backend interview.

---

# Goal

By the end of this chapter you should be able to answer:

- What is REST?
- REST vs HTTP
- REST Constraints
- HTTP Methods
- Idempotency
- Safe Methods
- Complete Spring Boot Request Lifecycle
- DispatcherServlet (High Level)

---

# 1. What is REST?

REST (Representational State Transfer) is an **architectural style** for designing web services.

> **Interview Definition**
>
> REST is an architectural style that uses HTTP protocols and resources to enable communication between client and server in a stateless manner.

Notice:

❌ REST is NOT a protocol.

❌ REST is NOT a framework.

✔ REST is an architectural style.

---

# Why REST?

Before REST, applications commonly used SOAP/XML web services.

Problems:

- Heavy XML payloads
- Complex standards
- Difficult to maintain
- Slow development

REST solved this by using:

- HTTP
- JSON
- Standard verbs (GET, POST...)
- Simple URLs

---

# REST Resource

Everything is treated as a **resource**.

Example:

```
User

Product

Order

Payment

Cart
```

Resources are identified using URIs.

Example

```
GET /users

GET /users/101

POST /orders

DELETE /products/20
```

Notice the URL represents a **noun**, not an action.

Good

```
GET /products

POST /orders
```

Bad

```
getProducts()

createOrder()

deleteProduct()
```

---

# REST Constraints (Interview Favorite)

There are six REST constraints. You don't need to memorize every detail, but you should know the important ones.

## 1. Client-Server

Frontend and backend should be independent.

```
React

↓

Spring Boot

↓

Database
```

Backend changes shouldn't require frontend redesign and vice versa.

---

## 2. Stateless ⭐⭐⭐⭐⭐

The most important REST constraint.

Every request must contain all the information needed.

Server should **not remember previous requests**.

Example

```
Request 1

Authorization: JWT

↓

Response

----------------

Request 2

Authorization: JWT

↓

Response
```

Each request is independent.

This makes REST APIs scalable.

---

## 3. Cacheable

Responses may be cached.

Example:

```
GET /products
```

Product catalog changes infrequently.

Caching improves performance.

---

## 4. Uniform Interface

Use predictable URLs and HTTP methods.

```
GET /users

POST /users

PUT /users/10

DELETE /users/10
```

---

# HTTP Methods

## GET

Retrieve data.

Example

```
GET /products
```

Should NOT modify data.

---

## POST

Create new resource.

```
POST /orders
```

Creates new record.

---

## PUT

Replace entire resource.

```
PUT /users/20
```

Think:

Complete Update.

---

## PATCH

Partial update.

```
PATCH /users/20
```

Only changed fields are updated.

---

## DELETE

Delete resource.

```
DELETE /orders/11
```

---

# Safe Methods

Safe means:

**No data modification.**

Safe:

```
GET

HEAD

OPTIONS
```

Unsafe:

```
POST

PUT

PATCH

DELETE
```

Interview Question:

Is PUT safe?

No.

It modifies data.

---

# Idempotency ⭐⭐⭐⭐⭐

Extremely common interview question.

Definition:

> Calling the same API multiple times produces the same final result.

---

## GET

```
GET /products
```

10 calls

↓

Same data

Idempotent ✅

---

## DELETE

```
DELETE /users/10
```

First call

↓

Deletes user

Second call

↓

Still deleted

Final state unchanged

Idempotent ✅

---

## PUT

```
PUT /users/10
```

Replace user.

Calling it multiple times gives the same final resource.

Idempotent ✅

---

## POST

```
POST /orders
```

Call once

↓

Order 1

Call again

↓

Order 2

Different outcome.

NOT Idempotent ❌

---

# REST API Flow

```
Browser

↓

HTTP Request

↓

Spring Boot

↓

Database

↓

JSON Response

↓

Browser
```

Simple.

But internally Spring performs many steps.

---

# Complete Spring Boot Request Lifecycle ⭐⭐⭐⭐⭐

This is one of the most important diagrams in backend interviews.

```
Client

↓

Embedded Tomcat

↓

DispatcherServlet

↓

Handler Mapping

↓

Controller

↓

Service

↓

Repository

↓

Database

↓

Repository

↓

Service

↓

Controller

↓

DispatcherServlet

↓

HTTP Response
```

Memorize this.

---

# What is DispatcherServlet?

Interview Definition:

> DispatcherServlet is the Front Controller of Spring MVC. It receives every incoming HTTP request, determines the appropriate controller, invokes it, and returns the response.

Think of it as the traffic police of your application.

Every request passes through it.

---

# Internal Request Flow

Suppose the client sends:

```
GET /products/15
```

Internally:

```
Tomcat receives request

↓

DispatcherServlet

↓

Find matching controller

↓

Invoke controller

↓

Controller calls Service

↓

Service calls Repository

↓

Repository queries Database

↓

Entity returned

↓

Jackson converts Java Object

↓

JSON Response
```

This is the complete execution path.

---

# Where Does Jackson Come In?

Controller returns:

```java
Product product;
```

Browser expects:

```json
{
  "id": 15,
  "name": "Laptop"
}
```

Jackson automatically converts:

```
Java Object

↓

JSON

↓

HTTP Response
```

This process is called **Serialization**.

The reverse (JSON → Java Object) is **Deserialization**.

---

# Production Example

Consider an e-commerce application.

Client requests:

```http
GET /api/products/100
```

Execution:

```
Tomcat

↓

DispatcherServlet

↓

ProductController

↓

ProductService

↓

ProductRepository

↓

MySQL

↓

Product Entity

↓

Jackson

↓

JSON

↓

Client
```

This is the exact flow you'll debug in production.

---

# Common Interview Questions

### What is REST?

Architectural style for designing stateless web services over HTTP.

---

### REST vs HTTP?

| REST | HTTP |
|------|------|
| Architectural Style | Communication Protocol |
| Defines API design principles | Defines message format and transport |
| Uses HTTP | Independent of REST |

---

### Why is REST Stateless?

To improve scalability, reliability, and simplify server-side request handling.

---

### Difference between PUT and PATCH?

PUT → Replace complete resource.

PATCH → Update only selected fields.

---

### Difference between POST and PUT?

POST

- Create

PUT

- Replace

Also,

POST is **not idempotent**.

PUT **is idempotent**.

---

### What is DispatcherServlet?

Front Controller that handles every incoming request and dispatches it to the correct controller.

---

# Common Mistakes

❌ Using verbs in URLs.

```
/createUser
```

Better:

```
POST /users
```

---

❌ Using POST for updates.

Prefer:

```
PUT

PATCH
```

---

❌ Returning entities directly.

Prefer DTOs (covered next).

---

❌ Not understanding request lifecycle.

Almost every Spring interview eventually reaches DispatcherServlet.

---

# Must Remember

✅ REST is an architectural style.

✅ HTTP is a protocol.

✅ Stateless is the most important REST constraint.

✅ GET is safe and idempotent.

✅ PUT is idempotent.

✅ POST is not idempotent.

✅ DispatcherServlet is the Front Controller.

✅ Request Flow:

```
Client

↓

Tomcat

↓

DispatcherServlet

↓

Controller

↓

Service

↓

Repository

↓

Database

↓

JSON Response
```

---

# What's Next?

**Response 2** covers everything you'll write daily as a backend developer:

- `@RestController`
- `@RequestMapping`
- `@GetMapping`
- `@PostMapping`
- `@PutMapping`
- `@DeleteMapping`
- `@RequestBody`
- `@PathVariable`
- `@RequestParam`
- DTOs
- `ResponseEntity`
- Jackson Internals
- Serialization vs Deserialization
- Top Interview Questions
