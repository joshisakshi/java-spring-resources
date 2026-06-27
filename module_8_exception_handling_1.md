# Module 8 - Exception Handling (Part 1)

# Exception Basics, try-catch-finally & Exception Hierarchy (`08-ExceptionHandling-Part1-Basics.md`)

> **Goal:** Understand why exceptions exist, the Java exception hierarchy, checked vs unchecked exceptions, `try`, `catch`, `finally`, `throw`, `throws`, execution flow, JVM behavior, and common interview questions.

---

# Table of Contents

1. What is an Exception?
2. Why Exception Handling?
3. Error vs Exception
4. Exception Hierarchy
5. Checked vs Unchecked Exceptions
6. try-catch
7. finally
8. throw
9. throws
10. Multi-catch
11. Execution Flow
12. JVM Internal Working
13. Stack Trace
14. Spring Boot Connection
15. Best Practices
16. Interview Questions
17. Exercises
18. Revision Sheet

---

# 1. What is an Exception?

An **Exception** is an event that interrupts the normal flow of program execution.

Example

```java
int a = 10;
int b = 0;

System.out.println(a / b);
```

Output

```text
Exception in thread "main"
java.lang.ArithmeticException: / by zero
```

The JVM creates an exception object and stops normal execution.

---

# Why Do We Need Exception Handling?

Without exception handling:

* Program terminates unexpectedly.
* Resources may not be released.
* Users receive poor error messages.

With exception handling:

* Recover gracefully.
* Continue execution where appropriate.
* Log useful information.
* Clean up resources.

---

# Real World Example

ATM Withdrawal

```text
Withdraw Money

↓

Insufficient Balance

↓

Show Error Message

↓

Continue Running
```

The ATM should not crash because one transaction failed.

---

# 2. Error vs Exception

Many beginners confuse these.

## Error

Represents serious problems that applications usually **should not handle**.

Examples

* OutOfMemoryError
* StackOverflowError
* VirtualMachineError

Generally caused by JVM or environment issues.

---

## Exception

Represents conditions that an application **can handle**.

Examples

* IOException
* SQLException
* FileNotFoundException
* ArithmeticException

---

# Exception Hierarchy

```text
                 Throwable
                 /      \
              Error   Exception
                        |
          ----------------------------
          |                          |
 Checked Exceptions      RuntimeException
                               |
                   Unchecked Exceptions
```

Everything that can be thrown extends `Throwable`.

---

# Common Checked Exceptions

* IOException
* SQLException
* ClassNotFoundException
* ParseException

Compiler forces you to handle them.

---

# Common Unchecked Exceptions

* NullPointerException
* ArithmeticException
* IndexOutOfBoundsException
* IllegalArgumentException
* NumberFormatException

Compiler does **not** force handling.

---

# 3. Checked vs Unchecked Exceptions

## Checked Exception

Must be handled or declared.

Example

```java
FileReader reader = new FileReader("data.txt");
```

Compilation fails unless handled.

```java
try{

    FileReader reader = new FileReader("data.txt");

}catch(IOException e){

}
```

or

```java
public void readFile() throws IOException{

}
```

---

## Unchecked Exception

```java
int a = 10 / 0;
```

Compiles successfully.

Fails only at runtime.

---

# Why Did Java Introduce Checked Exceptions?

To force developers to think about recoverable failures such as:

* Missing files
* Network failures
* Database failures

---

# 4. try-catch

Syntax

```java
try{

    // risky code

}catch(Exception e){

    // handling

}
```

Example

```java
try{

    int result = 10 / 0;

}catch(ArithmeticException e){

    System.out.println("Cannot divide by zero.");

}
```

Output

```text
Cannot divide by zero.
```

---

# Execution Flow

```text
Start

↓

try block

↓

Exception?

↓

Yes

↓

Matching catch

↓

Continue program
```

If no exception occurs:

```text
try

↓

skip catch

↓

continue
```

---

# Multiple Catch Blocks

```java
try{

}catch(IOException e){

}catch(SQLException e){

}catch(Exception e){

}
```

Always keep **more specific exceptions first**.

Wrong

```java
catch(Exception e){

}

catch(IOException e){

}
```

Compiler Error:

```text
Unreachable catch block
```

---

# Multi-Catch (Java 7)

```java
try{

}catch(IOException | SQLException e){

}
```

Useful when handling logic is the same.

---

# 5. finally

`finally` executes whether an exception occurs or not.

Example

```java
try{

    System.out.println("Try");

}catch(Exception e){

    System.out.println("Catch");

}finally{

    System.out.println("Finally");

}
```

Output

```text
Try
Finally
```

---

If exception occurs

Output

```text
Try
Catch
Finally
```

---

# Why finally?

Used for cleanup.

Examples

* Closing files
* Closing database connections
* Closing sockets
* Releasing locks

---

# When finally Does NOT Execute

Rare cases:

* `System.exit()`
* JVM crash
* Power failure

---

# 6. throw

Used to explicitly throw an exception.

```java
if(age < 18){

    throw new IllegalArgumentException("Age must be at least 18");

}
```

Execution stops immediately unless caught.

---

# throw vs throws

| throw                | throws                       |
| -------------------- | ---------------------------- |
| Throws one exception | Declares possible exceptions |
| Inside method        | Method signature             |
| Runtime statement    | Compile-time declaration     |

Example

```java
throw new IOException();
```

vs

```java
public void read() throws IOException{

}
```

---

# 7. throws

```java
public void loadFile() throws IOException{

}
```

Responsibility is transferred to the caller.

---

# Exception Propagation

```text
main()

↓

service()

↓

repository()

↓

FileReader()
```

If not handled,

the exception propagates upward through the call stack.

---

# JVM Internal Working

Example

```java
int result = 10 / 0;
```

Internally:

```text
JVM executes bytecode

↓

Division instruction

↓

Detect divide-by-zero

↓

Create ArithmeticException object

↓

Fill stack trace

↓

Search matching catch block

↓

If found → execute catch

Else → terminate thread
```

The JVM automatically creates the exception object.

---

# Stack Trace

Example

```text
Exception in thread "main"

java.lang.ArithmeticException: / by zero

at Calculator.divide(Calculator.java:15)

at Main.main(Main.java:5)
```

Meaning:

* Exception type
* Message
* Method
* File
* Line number
* Call hierarchy

Always read the **first relevant application line**.

---

# Memory Representation

```text
Stack

main()

↓

calculate()

↓

divide()

↓

ArithmeticException

Heap

ArithmeticException Object
```

Exceptions are regular Java objects stored in the Heap.

---

# Spring Boot Connection

Examples:

* Database failure
* Invalid request body
* Missing path variable
* Authentication failure
* Validation error

Spring converts exceptions into HTTP responses (we'll cover this in Part 3).

---

# Common Mistakes

❌ Catching `Exception` everywhere.

❌ Swallowing exceptions.

```java
catch(Exception e){

}
```

Never do this.

---

❌ Printing only

```java
e.printStackTrace();
```

in production.

Use proper logging instead.

---

❌ Using exceptions for normal control flow.

---

# Best Practices

* Catch the most specific exception possible.
* Log exceptions with context.
* Don't ignore exceptions.
* Use `finally` (or try-with-resources in the next part) for cleanup.
* Throw meaningful exceptions.

---

# Interview Questions

### What is an exception?

---

### Difference between Error and Exception?

---

### Difference between checked and unchecked exceptions?

---

### Difference between throw and throws?

---

### Does finally always execute?

---

### Can we have try without catch?

Yes, if followed by `finally`.

---

### Can we have try without finally?

Yes, if followed by at least one `catch`.

---

### Can finally exist without try?

No.

---

### Can we write multiple finally blocks?

No.

---

### Why are exceptions objects?

Because they extend `Throwable` and carry state such as the message, cause, and stack trace.

---

### Where are exception objects stored?

Heap.

---

# Exercises

1. Handle divide-by-zero using `try-catch`.
2. Demonstrate multiple `catch` blocks.
3. Demonstrate `throw` vs `throws`.
4. Draw the exception hierarchy.
5. Explain exception propagation using three nested method calls.

---

# Revision Sheet

* Exception
* Error
* Throwable
* Checked Exceptions
* Unchecked Exceptions
* try
* catch
* finally
* throw
* throws
* Multi-catch
* Exception Propagation
* Stack Trace
* JVM Exception Creation
* Spring Boot Connection
* Interview Questions
