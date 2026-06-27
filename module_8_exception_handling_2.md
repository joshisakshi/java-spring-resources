# Module 8 - Exception Handling (Part 2)

# Custom Exceptions, try-with-resources & Best Practices (`08-ExceptionHandling-Part2-Custom-Exceptions-and-Best-Practices.md`)

> **Goal:** Learn how to design custom exceptions, understand exception chaining, automatic resource management, suppressed exceptions, and production-ready exception handling practices.

---

# Table of Contents

1. Why Custom Exceptions?
2. Creating Custom Exceptions
3. Checked vs Unchecked Custom Exceptions
4. Exception Chaining
5. try-with-resources
6. AutoCloseable & Closeable
7. Suppressed Exceptions
8. Designing Exception Hierarchies
9. Logging Best Practices
10. Production Guidelines
11. Spring Boot Connection
12. Interview Questions
13. Exercises
14. Revision Sheet

---

# 1. Why Custom Exceptions?

Built-in exceptions are generic.

Example

```java
throw new Exception("Error");
```

This tells us very little.

Instead, create domain-specific exceptions.

Example

```text
UserNotFoundException

PaymentFailedException

InsufficientBalanceException

InvalidOrderException

DuplicateEmailException
```

These clearly describe what went wrong.

---

# Real Project Example

Instead of

```java
throw new RuntimeException("User not found");
```

Use

```java
throw new UserNotFoundException(userId);
```

Much easier to debug and maintain.

---

# 2. Creating a Custom Exception

## Checked Exception

Extend `Exception`.

```java
public class InvalidAgeException extends Exception{

    public InvalidAgeException(String message){

        super(message);

    }

}
```

Usage

```java
if(age < 18){

    throw new InvalidAgeException("Age must be at least 18");

}
```

Compiler forces handling.

---

## Unchecked Exception

Extend `RuntimeException`.

```java
public class UserNotFoundException extends RuntimeException{

    public UserNotFoundException(String message){

        super(message);

    }

}
```

Usage

```java
throw new UserNotFoundException("User not found");
```

No mandatory handling.

---

# Which Should You Choose?

| Situation                         | Recommendation                 |
| --------------------------------- | ------------------------------ |
| Recoverable business condition    | Checked Exception              |
| Programming error / invalid state | RuntimeException               |
| Spring Boot service layer         | RuntimeException (most common) |

Most modern Spring Boot applications prefer **custom RuntimeExceptions**.

---

# 3. Exception Constructors

Typical constructors

```java
public MyException(){}

public MyException(String message){}

public MyException(Throwable cause){}

public MyException(String message, Throwable cause){}
```

The last constructor is the most useful in production.

---

# 4. Exception Chaining

Sometimes one exception causes another.

Example

Database

↓

SQLException

↓

Service Layer

↓

UserServiceException

Instead of losing the original cause,

wrap it.

```java
try{

    repository.save(user);

}catch(SQLException e){

    throw new UserServiceException(
        "Unable to save user",
        e
    );

}
```

Now both:

* Business message
* Original stack trace

are preserved.

---

# Why Exception Chaining?

Without chaining

```text
UserServiceException
```

Original cause lost.

With chaining

```text
UserServiceException

↓

SQLException
```

Much easier debugging.

---

# Getting Original Cause

```java
exception.getCause();
```

Useful for logging.

---

# 5. try-with-resources

Before Java 7

```java
FileReader reader = null;

try{

    reader = new FileReader("data.txt");

}finally{

    if(reader != null){

        reader.close();

    }

}
```

Verbose and error-prone.

---

Modern Java

```java
try(FileReader reader =
        new FileReader("data.txt")){

    // read file

}
```

Resource closes automatically.

Cleaner and safer.

---

# Multiple Resources

```java
try(

    FileReader reader =
        new FileReader("input.txt");

    BufferedReader br =
        new BufferedReader(reader)

){

}
```

Resources close in **reverse order**.

---

# Internal Working

Compiler converts

```java
try(resource){

}
```

into code similar to

```java
try{

    ...

}finally{

    resource.close();

}
```

But it also correctly handles exceptions during `close()`.

---

# 6. AutoCloseable

Any class implementing

```java
AutoCloseable
```

can be used with try-with-resources.

Example

```java
public class DatabaseConnection
        implements AutoCloseable{

    @Override

    public void close(){

        System.out.println("Connection closed");

    }

}
```

Usage

```java
try(DatabaseConnection db =
        new DatabaseConnection()){

}
```

---

# Closeable vs AutoCloseable

| Closeable          | AutoCloseable        |
| ------------------ | -------------------- |
| Older              | Introduced in Java 7 |
| Only I/O           | Any resource         |
| Throws IOException | Throws Exception     |

---

# 7. Suppressed Exceptions

Interesting interview topic.

Suppose

```text
try block

↓

throws Exception A

↓

close()

↓

throws Exception B
```

Which exception should be reported?

Java reports

```text
Exception A
```

Exception B becomes a

```text
Suppressed Exception
```

Retrieve using

```java
exception.getSuppressed();
```

This prevents losing the original failure.

---

# 8. Designing Exception Hierarchies

Large applications often have

```text
ApplicationException

├── UserException

│     ├── UserNotFoundException

│     └── DuplicateUserException

├── PaymentException

│     ├── CardDeclinedException

│     └── InsufficientBalanceException

└── OrderException
```

Benefits

* Organized
* Easier handling
* Better logging

---

# 9. Logging Exceptions

Bad

```java
catch(Exception e){

    System.out.println(e);

}
```

Bad

```java
catch(Exception e){

    e.printStackTrace();

}
```

Production

```java
logger.error(
    "Unable to save user",
    e
);
```

Always log:

* Context
* Exception
* Stack trace

---

# Don't Swallow Exceptions

Wrong

```java
catch(Exception e){

}
```

This hides failures completely.

---

# Wrap or Rethrow?

Suppose Repository throws

```text
SQLException
```

Service layer should expose

```text
UserServiceException
```

Don't expose low-level implementation details unnecessarily.

---

# Don't Catch Exception Everywhere

Bad

```java
catch(Exception e){

}
```

Prefer

```java
catch(IOException e){

}
```

Specific exceptions communicate intent.

---

# Production Guidelines

✔ Create meaningful exception names.

✔ Preserve original cause.

✔ Avoid overly generic exceptions.

✔ Never ignore exceptions.

✔ Log once (avoid duplicate logging).

✔ Don't use exceptions for normal program flow.

---

# Spring Boot Connection

Example

Repository

↓

Throws

```text
SQLException
```

Service

↓

Throws

```text
UserCreationException
```

Controller

↓

Global Exception Handler

↓

HTTP 500

We'll build this completely in Part 3 using:

* `@ControllerAdvice`
* `@ExceptionHandler`
* Custom API error responses

---

# Common Mistakes

❌ Throwing `Exception`.

❌ Catching every exception.

❌ Losing the original cause.

❌ Forgetting try-with-resources.

❌ Printing stack traces in production.

❌ Using RuntimeException for everything without meaningful subclasses.

---

# Interview Questions

### Why create custom exceptions?

---

### Difference between checked and unchecked custom exceptions?

---

### What is exception chaining?

---

### Why preserve the cause?

---

### What is try-with-resources?

---

### Which interface enables try-with-resources?

`AutoCloseable`

---

### Difference between Closeable and AutoCloseable?

---

### What are suppressed exceptions?

---

### In what order are resources closed?

Reverse order.

---

### Why is try-with-resources preferred?

Cleaner, safer, automatic cleanup, proper handling of close failures.

---

### Why should exceptions be logged with context?

Without context, logs often become difficult to interpret in production systems.

---

# Exercises

1. Create `UserNotFoundException`.
2. Create `InvalidOrderException`.
3. Wrap an `IOException` inside a custom exception.
4. Write a custom `AutoCloseable` resource.
5. Demonstrate suppressed exceptions using two failing resources.
6. Design an exception hierarchy for an e-commerce application.

---

# Revision Sheet

* Custom Exceptions
* Checked Custom Exception
* Runtime Custom Exception
* Exception Constructors
* Exception Chaining
* getCause()
* try-with-resources
* AutoCloseable
* Closeable
* Suppressed Exceptions
* getSuppressed()
* Exception Hierarchy
* Logging
* Production Guidelines
* Spring Boot Usage
* Interview Questions
