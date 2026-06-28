---

# Interview Frequency

⭐⭐⭐⭐☆

Not asked as frequently as Singleton, but common in:

- Product Companies
- Spring Boot Backend Interviews
- Senior SDE-1 / SDE-2 Interviews
- REST API Discussions

Usually asked as follow-up questions.

---

# Goal

By the end of this chapter you should understand:

- Request Scope
- Session Scope
- Application Scope
- WebSocket Scope
- Scoped Proxies
- Injecting Short-Lived Beans into Singleton Beans
- Thread Safety
- Production Usage

---

# Table of Contents

1. Request Scope
2. Session Scope
3. Application Scope
4. WebSocket Scope
5. Scoped Proxy
6. Scope Comparison
7. Production Examples
8. Best Practices
9. Interview Questions

---

# 1. Request Scope

Definition:

A new bean instance is created **for every HTTP request**.

Example:

```java
@Component
@RequestScope
public class RequestContext {

}
```

Now imagine:

```
HTTP Request 1

↓

RequestContext A
```

```
HTTP Request 2

↓

RequestContext B
```

Different requests receive different objects.

---

# Lifecycle

```
HTTP Request Starts

↓

Create Bean

↓

Use Bean

↓

HTTP Response Sent

↓

Destroy Bean
```

Unlike Singleton,

Request Scope exists only during one request.

---

# Typical Use Cases

- Logged-in user information
- Request metadata
- Correlation IDs
- Temporary request cache
- Request-specific validation state

---

# 2. Session Scope

Definition:

One bean instance is created **per HTTP session**.

Example:

```java
@Component
@SessionScope
public class ShoppingCart {

}
```

Suppose:

```
User A

↓

ShoppingCart A
```

```
User B

↓

ShoppingCart B
```

Each user session receives its own object.

---

# Lifecycle

```
User Login

↓

Create Bean

↓

Multiple Requests

↓

Same Bean

↓

Logout / Session Timeout

↓

Destroy Bean
```

---

# Common Use Cases

- Shopping carts
- User preferences
- Wizard state
- Multi-step forms

---

# 3. Application Scope

Definition:

One bean is shared across the entire web application.

```
Entire Application

↓

Single Bean
```

This is similar to Singleton in many Spring Boot applications, but it is tied to the **ServletContext** rather than just the Spring container.

Typical use cases:

- Shared application metadata
- Global configuration
- Application statistics

---

# 4. WebSocket Scope

Rarely asked.

A bean lives as long as a WebSocket session.

Use cases:

- Live chat
- Stock tickers
- Real-time dashboards

For SDE-1 interviews, simply knowing that this scope exists is enough.

---

# 5. Injecting Request Scope into Singleton

One of the most common scenario-based questions.

Suppose:

```java
@Service
public class UserService {

    @Autowired

    private RequestContext context;

}
```

Problem:

```
Singleton

↓

Needs Request Bean

↓

Application Starts

↓

No HTTP Request Exists Yet
```

How can Spring inject something that doesn't exist?

---

# How Spring Solves It

Instead of injecting the real object,

Spring injects a **proxy**.

```
Singleton

↓

Proxy

↓

Actual Request Bean

↓

Current HTTP Request
```

When a request arrives,

the proxy looks up the correct Request-scoped bean for that request.

---

# Why Proxy?

Because the actual bean only exists during a request.

The singleton lives for the application's lifetime.

Without a proxy,

the dependency could not be resolved correctly.

---

# Scope Comparison

| Scope | Lifetime | Typical Usage |
|--------|----------|---------------|
| Singleton | Application (Spring Container) | Services, Repositories |
| Prototype | Every lookup | Builders, Generators |
| Request | One HTTP request | Request metadata |
| Session | One user session | Shopping cart |
| Application | ServletContext lifetime | Shared web resources |
| WebSocket | WebSocket session | Live messaging |

---

# Production Examples

### Singleton

```
PaymentService

OrderService

UserRepository

SecurityConfig
```

---

### Prototype

```
PDFGenerator

ExcelExporter

InvoiceBuilder
```

---

### Request

```
CurrentUserContext

RequestAudit

TraceContext
```

---

### Session

```
ShoppingCart

CheckoutState

LanguagePreference
```

---

# Thread Safety Discussion

Question:

Why are Request-scoped beans naturally safer than Singleton beans?

Answer:

Each request gets its own instance.

No two requests share the same object.

Therefore,

there is no shared mutable state between concurrent requests.

Singleton beans, on the other hand, are shared and should remain stateless.

---

# Best Practices

✅ Use Singleton for almost all services.

✅ Use Request Scope for request-specific data.

✅ Use Session Scope only when state must persist across multiple requests.

✅ Avoid storing user-specific state inside Singleton beans.

✅ Don't overuse Session Scope in stateless REST APIs.

---

# Interview Perspective

## Question

Why do we need Request Scope?

Excellent Answer:

> Request Scope allows Spring to create a fresh bean instance for every HTTP request. This is useful for storing request-specific state without worrying about thread safety or interference between concurrent users.

---

## Question

How can a Singleton bean use a Request-scoped bean?

Excellent Answer:

> Spring injects a scoped proxy rather than the actual Request-scoped bean. During each HTTP request, the proxy resolves the correct bean instance associated with the current request.

---

# Common Interview Traps

### Trap 1

Is Request Scope the same as Prototype?

❌ No.

Prototype creates a new bean every time it is requested from the container.

Request Scope creates one bean per HTTP request.

---

### Trap 2

Should Session Scope be used in REST APIs?

Generally, ❌ No.

Most REST APIs are stateless.

Session Scope is more common in traditional server-rendered web applications.

---

### Trap 3

Are Singleton and Application Scope identical?

Not exactly.

Singleton is managed by the Spring IoC Container.

Application Scope is tied to the ServletContext.

In a typical Spring Boot application with one application context, they often appear similar, but conceptually they are different.

---

# Summary

Remember:

- Request Scope → One bean per HTTP request.
- Session Scope → One bean per user session.
- Application Scope → One bean per web application.
- WebSocket Scope → One bean per WebSocket session.
- Scoped proxies allow shorter-lived beans to be used inside longer-lived beans.

---

# Revision Cheat Sheet

```
Singleton

↓

Entire Application

------------------

Prototype

↓

Every Lookup

------------------

Request

↓

Every HTTP Request

------------------

Session

↓

Every User Session

------------------

Application

↓

ServletContext

------------------

WebSocket

↓

Every WebSocket Session
```

---

# Exercises

1. Explain all Spring bean scopes.
2. Why can't Spring directly inject a Request-scoped bean into a Singleton bean?
3. What is a scoped proxy?
4. Why are Request-scoped beans naturally thread-safe?
5. In a stateless REST API, which bean scope would you choose for business services?

---

## End of Chapter 08

✅ Bean Scopes Completed

# Next Chapter

⭐⭐⭐⭐⭐ Component Scanning & Stereotype Annotations

We'll answer:

- How does Spring discover beans?
- What exactly does `@ComponentScan` do?
- What is the difference between `@Component`, `@Service`, `@Repository`, and `@Controller`?
- Are they functionally different or just semantic?
- How does Spring scan packages?
- How are BeanDefinitions created?
- What happens if a package isn't scanned?

This is another interview favorite because it ties together bean discovery, annotations, and container startup.