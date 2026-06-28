# Module 1 - Spring Fundamentals

# Chapter 10 - `@Configuration` & `@Bean`

## Part 2 — CGLIB, Full vs Lite Configuration & Spring Internals

---

# Interview Frequency

⭐⭐⭐⭐⭐ (High for Product Companies)

Asked In:

- Amazon
- Microsoft
- Atlassian
- Goldman Sachs
- JP Morgan
- Walmart

Common Questions:

- Why doesn't calling a `@Bean` method create multiple objects?
- What is CGLIB?
- What is `proxyBeanMethods`?
- Difference between Full and Lite Configuration?

---

# Goal

By the end of this chapter you should understand:

- Why `@Configuration` is special
- How Spring guarantees singleton beans
- CGLIB proxies
- Full vs Lite Configuration
- `proxyBeanMethods`
- Internal execution flow

---

# Table of Contents

1. The Problem
2. Calling `@Bean` Methods
3. CGLIB Proxy
4. Internal Flow
5. Full vs Lite Configuration
6. `proxyBeanMethods`
7. Best Practices
8. Interview Questions

---

# 1. The Problem

Consider the following configuration:

```java
@Configuration
public class AppConfig {

    @Bean
    public UserRepository userRepository() {
        return new UserRepository();
    }

    @Bean
    public UserService userService() {
        return new UserService(userRepository());
    }
}
```

Question:

How many `UserRepository` objects are created?

Many beginners answer:

```
Two
```

Reasoning:

```
userRepository()

↓

new UserRepository()
```

looks like it executes every time.

But Spring returns **only one** instance.

Why?

---

# Expected Singleton Behavior

```
UserService

↓

UserRepository

↓

Same Singleton Instance
```

Even though the method appears to create a new object.

---

# 2. What Really Happens?

Spring **does not use your configuration class directly.**

Instead,

it creates a subclass at runtime.

Conceptually:

```
Your AppConfig

↓

Spring Generates

↓

AppConfig$$EnhancerBySpringCGLIB
```

This generated class intercepts calls to `@Bean` methods.

---

# What is CGLIB?

## Definition

CGLIB (Code Generation Library) is a bytecode generation library used by Spring to create subclasses dynamically at runtime.

Instead of modifying your original class,

Spring creates:

```
AppConfig

↓

Generated Subclass

↓

Overrides @Bean Methods
```

This is why Spring can control bean creation.

---

# Internal Picture

```
            AppConfig
                │
                ▼
   AppConfig$$EnhancerBySpringCGLIB
                │
        Overrides userRepository()
                │
                ▼
      Check BeanFactory Cache
           │           │
         Found?       Not Found?
           │              │
           ▼              ▼
 Return Existing     Create Bean
     Bean            Register Bean
```

This is the key idea.

---

# 3. How Interception Works

Suppose Spring executes:

```java
userService();
```

Inside that method:

```java
userRepository();
```

is called.

Because the configuration class is actually a proxy,

the call is intercepted.

Conceptually:

```
Call userRepository()

↓

Proxy Intercepts

↓

Check Container

↓

Already Exists?

↓

Yes

↓

Return Existing Bean
```

So,

even though your code looks like:

```java
new UserRepository()
```

Spring returns the already managed singleton.

---

# Why Is This Needed?

Imagine Spring didn't intercept the call.

Execution would be:

```
userService()

↓

userRepository()

↓

new UserRepository()
```

Every call would create a new object.

That would violate Singleton scope.

---

# 4. Internal Startup Flow

During startup:

```
Find @Configuration

↓

Create CGLIB Proxy

↓

Read @Bean Methods

↓

Register BeanDefinitions

↓

Invoke Methods

↓

Store Singleton Beans

↓

Application Ready
```

Later,

every `@Bean` method call goes through the proxy.

---

# 5. Full vs Lite Configuration

Spring supports two modes.

## Full Configuration

```java
@Configuration
public class AppConfig {

}
```

Characteristics:

- CGLIB proxy created
- `@Bean` methods intercepted
- Singleton semantics preserved
- Recommended

---

## Lite Configuration

```java
@Component
public class AppConfig {

    @Bean
    public UserService userService() {
        ...
    }

}
```

Here:

- No CGLIB enhancement
- Direct Java method calls
- No interception of `@Bean` methods

This is called **Lite Configuration**.

---

# Comparison

| Feature | Full (`@Configuration`) | Lite (`@Component`) |
|---------|--------------------------|---------------------|
| CGLIB Proxy | ✅ | ❌ |
| Method Interception | ✅ | ❌ |
| Singleton Guarantee Between `@Bean` Methods | ✅ | ❌ |
| Recommended | ✅ | Only for specific cases |

---

# 6. `proxyBeanMethods`

Since Spring Boot 2.2,

`@Configuration` has an attribute:

```java
@Configuration(proxyBeanMethods = true)
```

This is the default.

Meaning:

```
Use CGLIB

↓

Intercept Calls

↓

Preserve Singleton
```

---

You can also write:

```java
@Configuration(proxyBeanMethods = false)
```

Meaning:

```
No Method Interception
```

Benefits:

- Faster startup
- Less proxy overhead

Trade-off:

If one `@Bean` method calls another directly,

Spring will **not** intercept that call.

You may accidentally create multiple objects.

---

# When Is `proxyBeanMethods = false` Safe?

Safe when:

- `@Bean` methods are independent.
- One `@Bean` method does **not** call another `@Bean` method.

This is increasingly common in modern Spring Boot auto-configuration.

---

# Real Project Example

```java
@Configuration(proxyBeanMethods = false)
public class JacksonConfig {

    @Bean
    public ObjectMapper objectMapper() {
        return new ObjectMapper();
    }

    @Bean
    public Validator validator() {
        return Validation.buildDefaultValidatorFactory()
                         .getValidator();
    }
}
```

Each bean is created independently.

No inter-bean method calls.

Using `proxyBeanMethods = false` is perfectly reasonable here.

---

# Best Practices

✅ Use `@Configuration` for configuration classes.

✅ Use `proxyBeanMethods = true` if `@Bean` methods depend on one another.

✅ Consider `proxyBeanMethods = false` for independent beans to reduce startup overhead.

✅ Don't manually instantiate configuration classes using `new`.

---

# Interview Perspective

## Question

Why doesn't calling a `@Bean` method create multiple singleton objects?

Excellent Answer:

> Spring enhances `@Configuration` classes using CGLIB. The generated proxy intercepts calls to `@Bean` methods, checks whether the bean already exists in the container, and returns the managed singleton instead of creating a new instance.

---

## Question

What is CGLIB?

Excellent Answer:

> CGLIB is a bytecode generation library used by Spring to create runtime subclasses. These subclasses intercept method calls, enabling features such as `@Configuration` method interception and many proxy-based framework capabilities.

---

## Question

What is the difference between Full and Lite Configuration?

Excellent Answer:

> Full Configuration uses `@Configuration`, enabling CGLIB enhancement and `@Bean` method interception. Lite Configuration uses classes such as `@Component` containing `@Bean` methods but does not provide method interception, so direct method calls behave like normal Java.

---

# Common Interview Traps

### Trap 1

Does every `@Bean` method create a new object?

❌ No.

Spring usually returns the managed bean from the container.

---

### Trap 2

Does `@Configuration` simply behave like `@Component`?

❌ No.

`@Configuration` adds CGLIB enhancement and special processing.

---

### Trap 3

Is `proxyBeanMethods = false` always better?

❌ No.

It improves startup time but should only be used when `@Bean` methods are independent.

---

# Summary

Remember:

- `@Configuration` classes are enhanced using CGLIB.
- CGLIB intercepts `@Bean` method calls.
- This preserves singleton behavior.
- `proxyBeanMethods = true` enables interception.
- Lite Configuration does not provide the same guarantees.

---

# Revision Cheat Sheet

```
@Configuration

↓

CGLIB Proxy

↓

Intercept @Bean Calls

↓

Check BeanFactory

↓

Existing Bean?

↓

Yes → Return Singleton

↓

No → Create & Register
```

---

# Exercises

1. Why doesn't calling a `@Bean` method repeatedly create multiple singleton objects?
2. Explain how CGLIB is used by Spring.
3. Compare Full and Lite Configuration.
4. When would you use `proxyBeanMethods = false`?
5. Draw the execution flow of a `@Bean` method in a `@Configuration` class.

---

## End of Chapter 10

# Next Chapter

**BeanFactory vs ApplicationContext**

We'll tie together everything you've learned about the IoC container and answer:

- What exactly is the Spring Container?
- Is `BeanFactory` still used?
- Why does Spring Boot use `ApplicationContext`?
- Internal hierarchy
- Container startup flow
- Interview comparisons
- Which one should you use in real projects?