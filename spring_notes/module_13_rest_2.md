# Module 3 — REST APIs (Response 2/3)

> **Interview Frequency:** ⭐⭐⭐⭐⭐ (Must Know)
>
> **Focus:** Building REST APIs in Spring Boot

---

# Goal

By the end of this chapter you should understand:

- @RestController
- @Controller vs @RestController
- Request Mapping annotations
- @PathVariable
- @RequestParam
- @RequestBody
- DTOs
- ResponseEntity
- Jackson Serialization & Deserialization
- Common interview questions

---

# 1. @RestController

## Interview Definition

`@RestController` is a stereotype annotation that marks a class as a REST controller. It combines:

```java
@Controller
+
@ResponseBody
```

Every method automatically returns the response body (typically JSON).

---

## Example

```java
@RestController
@RequestMapping("/products")
public class ProductController {

    @GetMapping("/{id}")
    public ProductDto getProduct(@PathVariable Long id) {
        return productService.getProduct(id);
    }
}
```

Spring automatically converts the returned object into JSON.

---

# @Controller vs @RestController ⭐⭐⭐⭐⭐

| @Controller | @RestController |
|-------------|-----------------|
| Returns View (JSP/Thymeleaf) | Returns JSON/XML |
| Used for MVC Applications | Used for REST APIs |
| Needs @ResponseBody for JSON | @ResponseBody included |

Interview Question:

**Why use `@RestController` instead of `@Controller`?**

Because backend APIs usually return JSON, not HTML pages.

---

# Request Mapping Annotations

Instead of writing:

```java
@RequestMapping(method = RequestMethod.GET)
```

Spring Boot provides shortcut annotations.

| Annotation | HTTP Method |
|------------|-------------|
| @GetMapping | GET |
| @PostMapping | POST |
| @PutMapping | PUT |
| @PatchMapping | PATCH |
| @DeleteMapping | DELETE |

These are just specialized versions of `@RequestMapping`.

---

# @RequestMapping

Defines the base URL.

```java
@RestController
@RequestMapping("/products")
public class ProductController {

}
```

Now every endpoint starts with:

```
/products
```

Example:

```
GET /products

POST /products

DELETE /products/10
```

---

# @PathVariable ⭐⭐⭐⭐⭐

Used when data is part of the URL.

Example

```
GET /products/25
```

Controller

```java
@GetMapping("/{id}")
public ProductDto getProduct(
        @PathVariable Long id) {

    return service.getProduct(id);

}
```

Spring extracts:

```
25

↓

id
```

---

## When to use PathVariable?

Use when identifying a specific resource.

Examples

```
/users/5

/orders/100

/products/22
```

---

# @RequestParam ⭐⭐⭐⭐☆

Used for query parameters.

Example

```
GET /products?page=1&size=20
```

Controller

```java
@GetMapping
public List<ProductDto> getProducts(

        @RequestParam int page,

        @RequestParam int size) {

}
```

Useful for:

- Pagination
- Sorting
- Filtering

---

# Difference

## PathVariable

```
/products/15
```

Resource Identification

---

## RequestParam

```
/products?page=2
```

Additional Information

---

| PathVariable | RequestParam |
|--------------|--------------|
| Mandatory | Often Optional |
| Identifies Resource | Filters/Options |
| URL Path | Query String |

---

# @RequestBody ⭐⭐⭐⭐⭐

Used when client sends JSON.

Request

```json
{
    "name":"Laptop",
    "price":65000
}
```

Controller

```java
@PostMapping
public ProductDto createProduct(

        @RequestBody ProductRequest request) {

    return service.create(request);

}
```

Internally:

```
JSON

↓

Jackson

↓

Java Object

↓

Controller
```

This conversion is called **Deserialization**.

---

# DTO (Data Transfer Object) ⭐⭐⭐⭐⭐

One of the most frequently asked backend topics.

## What is DTO?

A DTO is an object used only for transferring data between layers.

---

## Why not return Entity?

Suppose Entity

```java
@Entity
class User {

    Long id;

    String name;

    String password;

}
```

Returning Entity exposes:

```json
{
"id":1,
"name":"Alex",
"password":"secret"
}
```

Very bad.

---

## Better

```java
class UserResponse {

    Long id;

    String name;

}
```

Only required fields are returned.

---

## Production Flow

```
Client

↓

Controller

↓

DTO

↓

Service

↓

Entity

↓

Database
```

Entity remains inside the application.

DTO crosses API boundaries.

---

# ResponseEntity ⭐⭐⭐⭐⭐

Most production APIs return `ResponseEntity`.

Example

```java
@GetMapping("/{id}")

public ResponseEntity<ProductDto> getProduct(

        @PathVariable Long id){

    return ResponseEntity.ok(

            service.getProduct(id));

}
```

Benefits:

- Status Code
- Headers
- Body

All in one object.

---

## Common Responses

Success

```java
return ResponseEntity.ok(product);
```

Created

```java
return ResponseEntity.status(201)

                     .body(product);
```

No Content

```java
return ResponseEntity.noContent()

                     .build();
```

Not Found

```java
return ResponseEntity.notFound()

                     .build();
```

---

# Why not return Object directly?

Instead of

```java
return product;
```

Prefer

```java
return ResponseEntity.ok(product);
```

Because you control:

- HTTP Status
- Headers
- Cookies
- Response Body

Interviewers generally expect `ResponseEntity` in production code.

---

# Jackson Internals ⭐⭐⭐⭐☆

Suppose controller returns:

```java
ProductDto
```

Spring performs:

```
Controller

↓

DispatcherServlet

↓

HttpMessageConverter

↓

Jackson ObjectMapper

↓

JSON

↓

HTTP Response
```

Jackson is responsible for converting Java objects into JSON.

---

## Serialization

```
Java Object

↓

JSON
```

Used while sending response.

---

## Deserialization

```
JSON

↓

Java Object
```

Used while receiving request.

---

# Complete API Flow

```
POST /products

↓

Tomcat

↓

DispatcherServlet

↓

@RequestBody

↓

Jackson

↓

ProductRequest DTO

↓

Controller

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

Jackson

↓

JSON Response
```

This diagram combines almost everything you've learned so far.

---

# Production Best Practices

✅ Return DTOs, not Entities.

✅ Use `ResponseEntity`.

✅ Keep controllers thin.

✅ Business logic belongs in Service layer.

✅ Validation should happen on DTOs.

---

# Common Interview Questions

### Difference between @Controller and @RestController?

`@RestController` automatically returns JSON because it includes `@ResponseBody`.

---

### Difference between @PathVariable and @RequestParam?

PathVariable identifies a resource.

RequestParam provides optional query parameters like pagination or filtering.

---

### Why use DTO?

- Hide internal entities
- Better security
- API flexibility
- Prevent exposing database structure

---

### Why use ResponseEntity?

To control:

- HTTP Status
- Headers
- Response Body

---

### How does @RequestBody work internally?

```
HTTP Request

↓

DispatcherServlet

↓

HttpMessageConverter

↓

Jackson

↓

Java Object

↓

Controller
```

---

# Common Mistakes

❌ Returning Entity directly.

❌ Putting business logic inside Controller.

❌ Using PathVariable for filtering.

❌ Returning HTTP 200 for every response.

❌ Creating one DTO for every possible use case (keep DTOs focused on API contracts).

---

# Revision Sheet

```
@RestController

↓

@RequestMapping

↓

@GetMapping

@PostMapping

↓

@PathVariable

↓

@RequestParam

↓

@RequestBody

↓

DTO

↓

Service

↓

Entity

↓

Repository

↓

Database

↓

ResponseEntity

↓

Jackson

↓

JSON
```

---

# Next Response (Final Module 3)

We'll cover:

- Bean Validation (`@Valid`)
- Common Validation Annotations
- Global Exception Handling
- `@ControllerAdvice`
- `@ExceptionHandler`
- Standard Error Response Design
- Production REST API Best Practices
- Module 3 Cheat Sheet
- Top Interview Questions

This completes everything typically expected from REST APIs in Spring Boot interviews.