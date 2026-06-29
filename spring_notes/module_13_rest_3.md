# Module 3 — REST APIs (Response 3/3)

> **Interview Frequency:** ⭐⭐⭐⭐⭐ (Must Know)
>
> Companies: Google • Amazon • Walmart • Razorpay • PhonePe • Atlassian

---

# Goal

This chapter completes REST APIs by covering:

- Validation
- Global Exception Handling
- Error Responses
- REST API Best Practices
- Common Interview Questions
- Module 3 Revision

These topics are extremely common because companies want to know whether you can build **production-ready APIs**, not just CRUD endpoints.

---

# 1. Bean Validation

Imagine a client sends:

```json
{
    "name": "",
    "price": -100
}
```

Should this data reach the database?

No.

Validation should happen before business logic executes.

---

# @Valid ⭐⭐⭐⭐⭐

`@Valid` tells Spring to validate the request object before entering the controller.

```java
@PostMapping
public ResponseEntity<ProductResponse> createProduct(

        @Valid @RequestBody ProductRequest request){

    return ResponseEntity.ok(

            service.createProduct(request));
}
```

If validation fails,

Controller execution stops immediately.

---

# Common Validation Annotations

| Annotation | Purpose |
|------------|---------|
| @NotNull | Value cannot be null |
| @NotBlank | String cannot be null or empty |
| @NotEmpty | Collection/String cannot be empty |
| @Size | Length validation |
| @Min | Minimum value |
| @Max | Maximum value |
| @Email | Email format |
| @Pattern | Regex validation |

Example

```java
public class ProductRequest {

    @NotBlank
    private String name;

    @Min(1)
    private double price;

}
```

---

# Validation Flow

```
Client

↓

@RequestBody

↓

Jackson

↓

ProductRequest

↓

@Valid

↓

Validation Success?

↓

Yes

↓

Controller

↓

Service

↓

Database

--------------------

No

↓

400 Bad Request
```

Validation happens **before** your service layer is called.

---

# 2. Global Exception Handling ⭐⭐⭐⭐⭐

Without global handling:

```java
@GetMapping("/{id}")

public Product getProduct(Long id){

    try{

       ...

    }catch(Exception e){

       ...

    }

}
```

Every controller contains repeated `try-catch` blocks.

This is poor design.

---

# @ControllerAdvice

`@ControllerAdvice` handles exceptions for **all controllers** in the application.

Example

```java
@ControllerAdvice
public class GlobalExceptionHandler {

}
```

Think of it as a central place for exception handling.

---

# @ExceptionHandler

Used inside `@ControllerAdvice`.

```java
@ExceptionHandler(ProductNotFoundException.class)

public ResponseEntity<String> handle(

        ProductNotFoundException ex){

    return ResponseEntity

            .status(404)

            .body(ex.getMessage());

}
```

Whenever `ProductNotFoundException` occurs,

Spring automatically calls this method.

---

# Exception Flow

```
Client

↓

Controller

↓

Service

↓

Exception Thrown

↓

@ControllerAdvice

↓

@ExceptionHandler

↓

Error Response
```

No need for `try-catch` in every controller.

---

# Why is Global Exception Handling Better?

✅ Cleaner controllers

✅ Centralized logic

✅ Consistent error format

✅ Easier maintenance

---

# Standard Error Response

Instead of

```json
Product Not Found
```

Prefer

```json
{
    "timestamp":"2026-06-29T10:15:30Z",
    "status":404,
    "error":"NOT_FOUND",
    "message":"Product not found",
    "path":"/products/100"
}
```

Benefits:

- Easy debugging
- Consistent API
- Better client integration

---

# Common HTTP Status Codes

| Code | Meaning |
|------|---------|
| 200 | Success |
| 201 | Created |
| 204 | No Content |
| 400 | Bad Request |
| 401 | Unauthorized |
| 403 | Forbidden |
| 404 | Not Found |
| 409 | Conflict |
| 500 | Internal Server Error |

Interviewers often ask:

> **When would you return 400 vs 404?**

**400** → Invalid request data.

**404** → Resource does not exist.

---

# REST API Best Practices ⭐⭐⭐⭐⭐

## Good URL Design

Good

```
GET /products

POST /products

GET /products/10

DELETE /products/10
```

Bad

```
/getProducts

/deleteProduct

/createProduct
```

Use nouns, not verbs.

---

## Version APIs

```
/api/v1/products

/api/v2/products
```

Prevents breaking existing clients.

---

## Return Correct Status Codes

Don't always return:

```
200 OK
```

Use appropriate responses.

Example

Create

```
201 Created
```

Delete

```
204 No Content
```

Not Found

```
404
```

---

## Keep Controllers Thin

Bad

```
Controller

↓

Business Logic

↓

Database Logic
```

Good

```
Controller

↓

Service

↓

Repository
```

Controllers should mainly:

- Receive requests
- Validate input
- Call service
- Return response

---

## Never Expose Entities

Always use DTOs.

Reasons:

- Security
- Flexibility
- API stability
- Separation of concerns

---

# Most Asked Interview Questions

### Difference between @Valid and @Validated?

**@Valid**

- Standard Jakarta Bean Validation.
- Commonly used for validating request bodies.

**@Validated**

- Spring-specific.
- Supports validation groups and method-level validation.

For most REST APIs, `@Valid` is sufficient.

---

### Why use @ControllerAdvice?

To centralize exception handling and keep controllers clean.

---

### Why not use try-catch in every controller?

- Duplicate code
- Hard to maintain
- Inconsistent error responses

---

### Why validate DTO instead of Entity?

Because validation belongs at the API boundary.

Entities represent the persistence model, while DTOs represent the API contract.

---

### What happens if @Valid fails?

```
Request

↓

Validation

↓

Validation Exception

↓

@ControllerAdvice

↓

400 Bad Request
```

Controller and Service are **not executed**.

---

# Complete REST Request Lifecycle

This is the final diagram you should remember.

```
Client

↓

Tomcat

↓

DispatcherServlet

↓

Controller

↓

@RequestBody

↓

Jackson

↓

DTO

↓

Validation

↓

Service

↓

Repository

↓

Database

↓

Entity

↓

DTO

↓

ResponseEntity

↓

Jackson

↓

JSON

↓

Client
```

If an exception occurs:

```
Service

↓

Exception

↓

@ControllerAdvice

↓

Error Response
```

---

# Module 3 Cheat Sheet

## Request Flow

```
Client

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
```

---

## Important Annotations

```
@RestController

@RequestMapping

@GetMapping

@PostMapping

@RequestBody

@PathVariable

@RequestParam

@Valid

@ControllerAdvice

@ExceptionHandler
```

---

## Remember

✅ REST is Stateless.

✅ DispatcherServlet is the Front Controller.

✅ DTOs should cross API boundaries.

✅ Entities should stay inside the application.

✅ Validation happens before Controller logic executes.

✅ Use ResponseEntity for production APIs.

✅ Centralize exception handling with @ControllerAdvice.

---

# Module 3 Complete ✅

You are now comfortable with:

- REST fundamentals
- HTTP methods
- Request lifecycle
- DispatcherServlet
- Controllers
- DTOs
- Jackson
- Validation
- Exception handling
- Production REST API design

These concepts alone cover a large percentage of Spring Boot REST interview questions.

---

# Next Module

## Module 4 — Spring Data JPA & Hibernate

This is the **largest and most frequently asked module** after Spring Core.

We'll focus on:

- ORM
- Hibernate Architecture
- Entity Lifecycle ⭐⭐⭐⭐⭐
- Persistence Context ⭐⭐⭐⭐⭐
- EntityManager ⭐⭐⭐⭐⭐
- Dirty Checking ⭐⭐⭐⭐⭐
- Transactions ⭐⭐⭐⭐⭐
- Lazy vs Eager ⭐⭐⭐⭐⭐
- N+1 Problem ⭐⭐⭐⭐⭐

Understanding this module will prepare you for many of the deeper backend questions asked in product-based companies.