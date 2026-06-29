# Module 6 — Testing (Complete)

> **Interview Frequency:** ⭐⭐⭐⭐☆ (High)
>
> **Topics:** JUnit 5 • Mockito • @Mock • @InjectMocks • @MockBean • @WebMvcTest • @DataJpaTest • Integration Testing • Testcontainers

---

# Goal

By the end of this module you should understand:

- Why testing is important
- Unit vs Integration Tests
- JUnit 5
- Mockito
- Mock vs Spy
- Spring Boot Test Annotations
- Testcontainers (Interview Level)
- Testing Best Practices

For SDE-1/SDE-2 interviews, this covers **95% of commonly asked testing questions**.

---

# Why Testing?

Imagine deploying this method without testing:

```java
public double calculateDiscount(double amount) {
    return amount * 0.9;
}
```

A small bug could affect millions of users.

Testing helps ensure code correctness before deployment.

---

# Types of Tests

```
                UI Tests
                   ▲
           Integration Tests
                   ▲
             Unit Tests
```

The majority of backend interviews focus on:

- Unit Testing ⭐⭐⭐⭐⭐
- Integration Testing ⭐⭐⭐⭐☆

---

# Unit Testing ⭐⭐⭐⭐⭐

## Definition

Tests a single class or method in isolation.

Dependencies are mocked.

Example:

```
ProductService

↓

Mock ProductRepository
```

No real database.

No web server.

Very fast.

---

# Integration Testing ⭐⭐⭐⭐☆

## Definition

Tests multiple components working together.

Example:

```
Controller

↓

Service

↓

Repository

↓

Database
```

Uses the real Spring context.

Slower but more realistic.

---

# JUnit 5 ⭐⭐⭐⭐⭐

JUnit is the standard Java testing framework.

Example:

```java
@Test
void shouldAddNumbers() {

    assertEquals(5, 2 + 3);

}
```

Common Assertions:

```java
assertEquals()

assertTrue()

assertFalse()

assertNull()

assertNotNull()

assertThrows()
```

---

# Mockito ⭐⭐⭐⭐⭐

## Why Mockito?

Suppose:

```java
ProductService

↓

ProductRepository
```

You only want to test `ProductService`.

You don't want a real database.

Mockito creates fake objects.

---

# @Mock

Creates a fake dependency.

```java
@Mock
private ProductRepository repository;
```

No database calls occur.

---

# @InjectMocks

Creates the class under test and injects mocks.

```java
@InjectMocks
private ProductService service;
```

Execution:

```
Fake Repository

↓

Injected into

↓

ProductService
```

---

# Example

```java
@ExtendWith(MockitoExtension.class)
class ProductServiceTest {

    @Mock
    ProductRepository repository;

    @InjectMocks
    ProductService service;

}
```

---

# Mock vs Spy ⭐⭐⭐⭐☆

One of the most asked Mockito questions.

---

## Mock

Everything is fake.

```java
@Mock
ProductService service;
```

Methods return default values unless stubbed.

---

## Spy

Wraps a real object.

```java
@Spy
ArrayList<String> list;
```

Real methods execute unless overridden.

---

# Mock vs Spy

| Mock | Spy |
|------|-----|
| Completely Fake | Real Object |
| No real logic | Real methods run |
| Preferred for unit tests | Used selectively |

Interview Answer:

> Prefer `@Mock` for unit tests. Use `@Spy` only when you want partial mocking.

---

# Stubbing

Mockito allows defining expected behavior.

```java
when(repository.findById(1L))

.thenReturn(Optional.of(product));
```

Now:

```
repository.findById(1)
```

returns the predefined object instead of querying a database.

---

# Verification

Verify interactions.

```java
verify(repository)

.save(product);
```

Ensures the method was actually called.

---

# @SpringBootTest ⭐⭐⭐⭐⭐

Loads the entire Spring application.

```
Controller

↓

Service

↓

Repository

↓

Spring Context
```

Best for:

Integration Testing.

Drawback:

Slower startup.

---

# @WebMvcTest ⭐⭐⭐⭐☆

Loads only the web layer.

```
Controller

↓

MockMvc

↓

Mock Service
```

No database.

No repository.

Perfect for testing REST controllers.

---

# MockMvc

Simulates HTTP requests without starting a real server.

Example:

```java
mockMvc.perform(get("/products"))

.andExpect(status().isOk());
```

Very common in Spring Boot projects.

---

# @DataJpaTest ⭐⭐⭐⭐☆

Loads only JPA components.

```
Repository

↓

Hibernate

↓

Embedded Database
```

Useful for testing:

- Repository methods
- JPQL
- Entity mappings

---

# @MockBean

Frequently asked in Spring Boot interviews.

Difference:

`@Mock`

- Pure Mockito
- Unit tests

`@MockBean`

- Spring Boot
- Replaces a bean inside the Spring context

Example:

```java
@WebMvcTest

class ProductControllerTest {

    @MockBean
    ProductService service;

}
```

The controller receives the mocked service bean.

---

# Testcontainers ⭐⭐⭐⭐☆

Modern backend teams increasingly use Testcontainers.

Instead of:

```
H2 Database
```

Run a real database in Docker.

Example:

```
JUnit

↓

Docker

↓

PostgreSQL

↓

Repository Tests
```

Advantages:

- Real database behavior
- More reliable integration tests
- Eliminates "works on my machine" issues

Interview expectation:

Know the concept; you don't need to memorize setup code.

---

# Testing Pyramid

```
        Few
    UI Tests

   Integration

Many Unit Tests
```

Most tests should be unit tests because they are:

- Fast
- Cheap
- Easy to maintain

---

# Production Best Practices

## Unit Test Business Logic

Focus on:

- Service layer
- Utility classes

---

## Mock External Dependencies

Never call:

- Database
- Payment Gateway
- Email Service
- Third-party APIs

in unit tests.

---

## Keep Tests Independent

Each test should run independently.

No test should depend on another.

---

## Test Behavior, Not Implementation

Instead of testing private methods,

test observable outcomes.

---

## Meaningful Test Names

Good:

```java
shouldReturnProductWhenIdExists()
```

Bad:

```java
test1()
```

---

# Common Interview Questions

### Difference between Unit and Integration Testing?

Unit tests isolate a single class using mocks.

Integration tests verify multiple components working together.

---

### Why Mockito?

To replace dependencies with mock objects for isolated testing.

---

### Difference between @Mock and @MockBean?

| @Mock | @MockBean |
|--------|-----------|
| Mockito | Spring Boot |
| No Spring Context | Spring Context |
| Unit Tests | Integration/Web Tests |

---

### Difference between Mock and Spy?

Mock is completely fake.

Spy wraps a real object and allows partial mocking.

---

### Why MockMvc?

To test REST controllers without starting a real HTTP server.

---

### Why Testcontainers?

To run integration tests against a real database inside Docker.

---

# Common Mistakes

❌ Writing integration tests for everything.

❌ Using real databases in unit tests.

❌ Testing private methods directly.

❌ Sharing state between tests.

❌ Depending on test execution order.

---

# Module 6 Cheat Sheet

## Unit Test

```
Service

↓

Mock Repository
```

---

## Integration Test

```
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
@Test

@Mock

@InjectMocks

@MockBean

@SpringBootTest

@WebMvcTest

@DataJpaTest
```

---

## Mockito

```
when()

thenReturn()

verify()
```

---

## Remember

✅ Unit Tests are fast.

✅ Integration Tests verify component interaction.

✅ Mock = Fake object.

✅ Spy = Real object with partial mocking.

✅ @MockBean replaces Spring beans.

✅ MockMvc tests controllers.

✅ Testcontainers use real databases.

---

# Module 6 Complete ✅

You now understand:

- Unit Testing
- Integration Testing
- JUnit 5
- Mockito
- Mock vs Spy
- @Mock
- @InjectMocks
- @MockBean
- MockMvc
- @WebMvcTest
- @DataJpaTest
- Testcontainers

This is sufficient for almost all SDE-1/SDE-2 Spring Boot testing interviews.

---

# Next Module

## Module 7 — Spring Boot Internals (3 Responses)

This is the **highest ROI advanced module** and often differentiates strong candidates.

We'll cover:

### Response 1 ⭐⭐⭐⭐⭐
- Spring Boot Startup
- Bean Creation Lifecycle
- IoC Container Internals
- Dependency Injection Internals

### Response 2 ⭐⭐⭐⭐⭐
- DispatcherServlet Internals
- Auto Configuration
- Circular Dependency
- BeanPostProcessor

### Response 3 ⭐⭐⭐⭐⭐
- AOP
- Dynamic Proxies
- @Transactional Internals
- Spring Interview Deep-Dive Questions

> **If you master Module 7, you'll be able to answer many "how does Spring work internally?" questions that impress interviewers.**