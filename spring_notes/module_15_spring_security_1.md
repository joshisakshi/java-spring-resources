# Module 5 — Spring Security (Response 1/2)

> **Interview Frequency:** ⭐⭐⭐⭐⭐ (Must Know)
>
> **Topics:** Authentication • Authorization • Security Filter Chain • JWT • Request Lifecycle

---

# Goal

By the end of this chapter, you should understand:

- Why Spring Security exists
- Authentication vs Authorization
- Spring Security Architecture
- Security Filter Chain
- JWT Authentication
- Complete Request Flow

This is one of the most common backend interview topics.

---

# Why Spring Security?

Suppose your application has:

```
POST /login

GET /users

GET /payments

POST /transferMoney
```

Without security,

anyone can access them.

We need:

- Identity verification
- Access control
- Password protection
- Secure API communication

Spring Security solves these problems.

---

# Authentication vs Authorization ⭐⭐⭐⭐⭐

This is probably the **most asked Spring Security question**.

---

## Authentication

Authentication answers:

> **Who are you?**

Example

```
Username

Password

↓

Verify Identity

↓

Authenticated
```

---

## Authorization

Authorization answers:

> **What are you allowed to do?**

Example

```
Authenticated User

↓

Role = ADMIN

↓

Can Delete User
```

Another user:

```
Role = CUSTOMER

↓

Cannot Delete User
```

---

# Easy Way to Remember

```
Authentication

↓

Identity

-------------------

Authorization

↓

Permission
```

Interview Answer:

> Authentication verifies identity. Authorization determines permissions after successful authentication.

---

# Spring Security Architecture

```
Client

↓

Security Filter Chain

↓

Authentication

↓

Authorization

↓

DispatcherServlet

↓

Controller

↓

Service

↓

Database
```

Notice:

Security executes **before** your controller.

---

# Security Filter Chain ⭐⭐⭐⭐⭐

This is the heart of Spring Security.

Interview Definition:

> The Security Filter Chain is a sequence of servlet filters that intercept every HTTP request before it reaches the application.

Think of it like airport security.

```
Passenger

↓

Security Check

↓

Board Flight
```

Similarly,

```
Request

↓

Security Filters

↓

Controller
```

If authentication fails,

the request never reaches your controller.

---

# High-Level Filter Flow

```
HTTP Request

↓

Security Filter Chain

↓

Authentication Filter

↓

Authorization Filter

↓

DispatcherServlet

↓

Controller
```

There are many filters internally, but for interviews this mental model is enough.

---

# JWT (JSON Web Token) ⭐⭐⭐⭐⭐

Most modern Spring Boot APIs are **stateless**.

Instead of storing sessions on the server,

the client carries proof of authentication in a JWT.

---

# JWT Authentication Flow

```
Client

↓

POST /login

↓

Username + Password

↓

Spring Security

↓

Credentials Verified

↓

Generate JWT

↓

Return Token
```

Subsequent requests:

```
GET /products

Authorization:

Bearer <JWT>

↓

Spring Security

↓

Validate JWT

↓

Allow Request
```

---

# Why JWT?

Traditional Session Authentication:

```
Login

↓

Server stores Session

↓

Client sends Session ID
```

Problem:

Server must remember every user session.

---

JWT:

```
Login

↓

JWT Generated

↓

Client Stores Token

↓

Every Request Sends Token
```

Server remains **stateless**.

Better for:

- Microservices
- Cloud deployments
- Horizontal scaling

---

# JWT Structure (Interview Basic)

A JWT has three parts:

```
Header

.

Payload

.

Signature
```

Example

```
xxxxx.yyyyy.zzzzz
```

You do **not** need to memorize the encoding format for interviews.

Just know:

- Header → algorithm information
- Payload → user information (claims)
- Signature → prevents tampering

---

# Complete JWT Request Flow

```
Client

↓

Login Request

↓

Authentication

↓

JWT Generated

↓

Client Stores JWT

↓

Every Future Request

↓

Authorization Header

↓

JWT Validation

↓

Controller
```

---

# What Happens Internally?

Request

```
GET /orders
```

Headers

```
Authorization:

Bearer eyJhbGc...
```

Execution

```
Tomcat

↓

Security Filter Chain

↓

Extract JWT

↓

Validate Signature

↓

Check Expiration

↓

Load User Details

↓

Authentication Successful

↓

DispatcherServlet

↓

Controller
```

If validation fails:

```
401 Unauthorized
```

Controller is never executed.

---

# Session vs JWT

| Session | JWT |
|---------|-----|
| Server stores session | Client stores token |
| Stateful | Stateless |
| Better for traditional MVC | Better for REST APIs & Microservices |

Interview Answer:

> Modern REST APIs generally prefer JWT because it supports stateless authentication and scales better.

---

# Common Interview Questions

### Authentication vs Authorization?

Authentication verifies identity.

Authorization determines permissions.

---

### What is Spring Security?

A framework that provides authentication, authorization, and protection against common security attacks.

---

### What is the Security Filter Chain?

A chain of servlet filters that intercepts every request before it reaches the controller.

---

### Why JWT?

To implement stateless authentication where the client carries the authentication token.

---

### Why is JWT preferred in Microservices?

Because servers don't need to store session state, making horizontal scaling much easier.

---

# Common Mistakes

❌ Confusing Authentication with Authorization.

❌ Storing sensitive information (like passwords) inside JWT payloads.

❌ Assuming JWTs are encrypted by default (they are signed, not necessarily encrypted).

❌ Thinking Security starts at the Controller. It starts at the Filter Chain.

---

# Revision Sheet

## Authentication Flow

```
Login

↓

Verify Credentials

↓

Generate JWT

↓

Return Token
```

---

## Request Flow

```
Client

↓

Security Filter Chain

↓

Authentication

↓

Authorization

↓

DispatcherServlet

↓

Controller

↓

Service

↓

Repository
```

---

## Remember

✅ Authentication = Identity

✅ Authorization = Permission

✅ JWT = Stateless Authentication

✅ Security Filter Chain executes before DispatcherServlet

✅ Invalid JWT → 401 Unauthorized

---

# Next Response

We'll complete Spring Security with:

- `UserDetailsService`
- `UserDetails`
- `PasswordEncoder`
- BCrypt
- OAuth2 (Interview Summary)
- Spring Security Configuration
- Production Best Practices
- Top Security Interview Questions
- Module 5 Cheat Sheet

This will complete everything typically expected from Spring Security for SDE-1/SDE-2 backend interviews.