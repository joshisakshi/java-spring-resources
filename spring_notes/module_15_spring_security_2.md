# Module 5 — Spring Security (Response 2/2)

> **Interview Frequency:** ⭐⭐⭐⭐⭐ (Must Know)
>
> **Topics:** UserDetails • UserDetailsService • PasswordEncoder • BCrypt • OAuth2 • Security Configuration

---

# Goal

By the end of this chapter, you should understand:

- UserDetails
- UserDetailsService
- PasswordEncoder
- BCrypt
- Security Configuration
- OAuth2 (Interview Level)
- Complete Authentication Flow
- Production Best Practices

This completes everything expected from **Spring Security** for SDE-1/SDE-2 interviews.

---

# Authentication Flow Recap

```
Client

↓

POST /login

↓

Security Filter Chain

↓

Authentication Manager

↓

UserDetailsService

↓

Database

↓

Password Verification

↓

JWT Generated

↓

Client Stores JWT
```

Every request after login:

```
Client

↓

Bearer JWT

↓

Security Filter Chain

↓

JWT Validation

↓

Controller
```

---

# UserDetails ⭐⭐⭐⭐⭐

## What is UserDetails?

**Interview Definition**

`UserDetails` is a Spring Security interface that represents the authenticated user's information.

It contains:

- Username
- Password
- Roles
- Account Status

Think of it as Spring Security's internal representation of a logged-in user.

---

## Internal Fields

```
Username

Password

Authorities (Roles)

Account Expired?

Account Locked?

Credentials Expired?

Enabled?
```

---

# UserDetailsService ⭐⭐⭐⭐⭐

Authentication requires loading a user.

Who performs this?

`UserDetailsService`

---

## Interview Definition

`UserDetailsService` is an interface responsible for loading user information during authentication.

Main method:

```java
UserDetails loadUserByUsername(String username);
```

Spring Security calls this automatically during login.

---

# Internal Flow

```
Login Request

↓

Username

↓

UserDetailsService

↓

Database Query

↓

User Entity

↓

Convert to UserDetails

↓

Authentication Manager
```

Notice:

You never call `loadUserByUsername()` directly.

Spring Security does.

---

# Typical Implementation

```java
@Service
public class CustomUserDetailsService
        implements UserDetailsService {

    @Override
    public UserDetails loadUserByUsername(
            String username) {

        User user =
            repository.findByEmail(username);

        return new CustomUserDetails(user);
    }
}
```

In production, the service usually loads users from a database.

---

# PasswordEncoder ⭐⭐⭐⭐⭐

One of the easiest interview questions.

Never store passwords like this:

```
password123
```

If the database is compromised,

every password is exposed.

---

# Password Hashing

Instead,

store:

```
$2a$10$L6...
```

This is a **hash**, not the original password.

Hashing is one-way.

You cannot recover the original password.

---

# BCryptPasswordEncoder ⭐⭐⭐⭐⭐

Spring Security recommends BCrypt.

Example

```java
PasswordEncoder encoder =
        new BCryptPasswordEncoder();

String hash =
        encoder.encode("password123");
```

Verification

```java
encoder.matches(
        "password123",
        hash);
```

Never compare passwords using:

```java
equals()
```

Always use `PasswordEncoder.matches()`.

---

# Why BCrypt?

Advantages:

- Salt generated automatically
- Slow by design (harder to brute force)
- Industry standard
- Built into Spring Security

Interview Answer:

> BCrypt is preferred because it automatically salts passwords and is computationally expensive, making brute-force attacks more difficult.

---

# Authentication Internals

Suppose the user logs in.

```
Username

↓

Database

↓

Stored BCrypt Hash

↓

PasswordEncoder.matches()

↓

Authenticated?
```

Spring **does not decrypt** the stored password.

It hashes the incoming password and compares securely.

---

# Security Configuration ⭐⭐⭐⭐☆

In modern Spring Security (Spring Boot 3+), security is configured using a `SecurityFilterChain` bean.

Example (simplified):

```java
@Bean
SecurityFilterChain securityFilterChain(
        HttpSecurity http)
        throws Exception {

    http
        .csrf(csrf -> csrf.disable())
        .authorizeHttpRequests(auth -> auth
            .requestMatchers("/login").permitAll()
            .anyRequest().authenticated()
        );

    return http.build();
}
```

Interview Tip:

You do **not** need to memorize the DSL syntax.

Understand:

- Public endpoints
- Protected endpoints
- Filter chain configuration

---

# OAuth2 (Interview Summary) ⭐⭐⭐⭐☆

Interviewers usually expect a conceptual understanding.

---

## Problem

Instead of creating new accounts,

users click:

```
Continue with Google

Continue with GitHub

Continue with Microsoft
```

---

## OAuth2 Flow

```
Client

↓

Google Login

↓

Google Verifies User

↓

Google Returns Token

↓

Your Application Trusts Google

↓

User Logged In
```

Your application never sees the user's Google password.

---

# JWT vs OAuth2

| JWT | OAuth2 |
|------|---------|
| Token Format | Authorization Framework |
| Used for authentication tokens | Used to delegate authentication/authorization |
| Usually issued by your backend | Often involves third-party identity providers |

Interview Tip:

OAuth2 and JWT are **not competitors**.

Many systems use OAuth2 **to obtain** a JWT.

---

# Complete Spring Security Flow

```
HTTP Request

↓

Security Filter Chain

↓

Authentication Filter

↓

JWT Validation

↓

Authentication Manager

↓

UserDetailsService

↓

Database

↓

UserDetails

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

This is the architecture interviewers love to see.

---

# Production Best Practices

## Use BCrypt

Never store plain-text passwords.

---

## Keep JWT Small

Store:

- User ID
- Username
- Roles

Avoid:

- Passwords
- Sensitive personal data

---

## HTTPS Only

Always send JWTs over HTTPS.

Never expose tokens over unencrypted connections.

---

## Short Token Expiry

Typical access token lifetime:

```
15–60 minutes
```

Use refresh tokens for longer sessions.

---

## Principle of Least Privilege

Grant only the permissions a user actually needs.

Avoid giving broad administrative access.

---

# Common Interview Questions

### What is UserDetails?

A Spring Security interface representing an authenticated user.

---

### What is UserDetailsService?

An interface used to load user information during authentication.

---

### Why BCrypt?

Because it hashes passwords securely with automatic salting and is resistant to brute-force attacks.

---

### Does Spring Security decrypt passwords?

No.

Passwords are hashed and verified using `PasswordEncoder.matches()`.

---

### JWT vs Session?

Session:

- Server stores state.

JWT:

- Client stores token.
- Server remains stateless.

---

### OAuth2 vs JWT?

OAuth2 is an authorization framework.

JWT is a token format often used within OAuth2-based systems.

---

### Where does authentication happen?

Inside the **Security Filter Chain**, before the request reaches the controller.

---

# Common Mistakes

❌ Storing plain-text passwords.

❌ Putting passwords inside JWT payloads.

❌ Returning sensitive user information after login.

❌ Assuming OAuth2 replaces JWT.

❌ Writing custom password hashing instead of using `PasswordEncoder`.

---

# Module 5 Cheat Sheet

## Authentication

```
Username

↓

UserDetailsService

↓

Database

↓

UserDetails

↓

PasswordEncoder

↓

Authenticated
```

---

## Authorization

```
Role

↓

Permission Check

↓

Allow / Deny
```

---

## Request Flow

```
Client

↓

Security Filter Chain

↓

JWT Validation

↓

Authentication

↓

Authorization

↓

DispatcherServlet

↓

Controller
```

---

## Remember

✅ Authentication = Identity

✅ Authorization = Permission

✅ UserDetails represents the logged-in user

✅ UserDetailsService loads the user

✅ BCrypt hashes passwords

✅ JWT enables stateless authentication

✅ Security Filter Chain executes before controllers

---

# Module 5 Complete ✅

You now understand:

- Authentication
- Authorization
- Security Filter Chain
- JWT
- UserDetails
- UserDetailsService
- PasswordEncoder
- BCrypt
- OAuth2 (Interview Level)
- Security Configuration
- Production Security Best Practices

These topics cover the vast majority of Spring Security questions asked in backend interviews.

---

# Next Module

## Module 6 — Testing (Single Response)

We'll cover only the interview-relevant topics:

- JUnit 5 ⭐⭐⭐⭐⭐
- Mockito ⭐⭐⭐⭐⭐
- Mock vs Spy
- @Mock
- @InjectMocks
- @MockBean
- @WebMvcTest
- @DataJpaTest
- Integration Testing
- Testcontainers (Interview Summary)
- Most Asked Testing Questions

> This module is intentionally concise because interviewers typically focus on practical testing concepts rather than exhaustive testing APIs.