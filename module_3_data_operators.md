# Module 3 - Operators (`03-Operators.md`)

# Operators in Java

> **Goal:** Learn all Java operators, precedence, associativity, short-circuit evaluation, interview tricks, and production usage.

---

# Table of Contents

1. What is an Operator?
2. Arithmetic Operators
3. Unary Operators
4. Relational Operators
5. Logical Operators
6. Bitwise Operators
7. Shift Operators
8. Assignment Operators
9. Ternary Operator
10. Operator Precedence
11. Interview Questions
12. Best Practices
13. Exercises

---

# 1. What is an Operator?

An **operator** performs an operation on one or more operands.

```java
int sum = 10 + 20;
```

* `+` → Operator
* `10`, `20` → Operands

---

# Types of Operators

* Arithmetic
* Unary
* Relational
* Logical
* Bitwise
* Shift
* Assignment
* Ternary
* instanceof

---

# 2. Arithmetic Operators

| Operator | Meaning        |
| -------- | -------------- |
| +        | Addition       |
| -        | Subtraction    |
| *        | Multiplication |
| /        | Division       |
| %        | Modulus        |

Example

```java
int a = 10;
int b = 3;

System.out.println(a + b); //13
System.out.println(a - b); //7
System.out.println(a * b); //30
System.out.println(a / b); //3
System.out.println(a % b); //1
```

---

## Integer Division

```java
System.out.println(5 / 2);
```

Output

```
2
```

Reason:

Both operands are integers.

---

## Floating Point Division

```java
System.out.println(5.0 / 2);
```

Output

```
2.5
```

---

# 3. Unary Operators

| Operator | Meaning     |
| -------- | ----------- |
| +        | Unary Plus  |
| -        | Unary Minus |
| ++       | Increment   |
| --       | Decrement   |
| !        | Logical NOT |

---

## Pre Increment

```java
int x = 5;

System.out.println(++x);
```

Output

```
6
```

Increment first.

---

## Post Increment

```java
int x = 5;

System.out.println(x++);
```

Output

```
5
```

Then x becomes

```
6
```

---

## Interview Question

```java
int a = 5;

int b = a++;

System.out.println(a);
System.out.println(b);
```

Output

```
6
5
```

---

# 4. Relational Operators

Used for comparison.

| Operator | Meaning       |
| -------- | ------------- |
| ==       | Equal         |
| !=       | Not Equal     |
| >        | Greater       |
| <        | Less          |
| >=       | Greater Equal |
| <=       | Less Equal    |

Example

```java
int age = 20;

System.out.println(age >= 18);
```

Output

```
true
```

---

# 5. Logical Operators

Operate on boolean values.

| Operator | Meaning |   |    |
| -------- | ------- | - | -- |
| &&       | AND     |   |    |
|          |         |   | OR |
| !        | NOT     |   |    |

Example

```java
boolean loggedIn = true;
boolean admin = false;

System.out.println(loggedIn && admin);
```

Output

```
false
```

---

# Short Circuit Evaluation

```java
if(age > 18 && salary > 50000)
```

If first condition is false,

Second condition is **never evaluated**.

This improves performance.

---

## Difference

```java
&&
```

Short circuit.

```java
&
```

Always evaluates both operands.

Same for

```java
||
```

vs

```java
|
```

---

# 6. Bitwise Operators

Operate at binary level.

| Operator | Meaning |
| -------- | ------- |
| &        | AND     |
| |        | OR      |
| ^        | XOR     |
| ~        | NOT     |

Example

```
5 = 0101

3 = 0011

AND

0001
```

Result

```
1
```

Mostly used in:

* Encryption
* Compression
* Permissions
* Competitive Programming

---

# 7. Shift Operators

| Operator | Meaning              |
| -------- | -------------------- |
| <<       | Left Shift           |
| >>       | Right Shift          |
| >>>      | Unsigned Right Shift |

Example

```java
System.out.println(5 << 1);
```

Binary

```
0101

↓

1010
```

Output

```
10
```

---

# 8. Assignment Operators

Basic

```java
int x = 5;
```

Compound

```java
x += 2;

x -= 2;

x *= 2;

x /= 2;

x %= 2;
```

Equivalent

```java
x = x + 2;
```

---

# 9. Ternary Operator

Syntax

```java
condition ? value1 : value2;
```

Example

```java
int age = 20;

String result = age >= 18 ? "Adult" : "Minor";
```

Cleaner than

```java
if-else
```

for simple decisions.

---

# 10. instanceof Operator

Checks object type.

```java
Object obj = "Hello";

System.out.println(obj instanceof String);
```

Output

```
true
```

Used frequently in frameworks.

---

# Operator Precedence

Highest → Lowest

```
()

Unary

*, /, %

+, -

<< >>

< <= > >=

== !=

&

^

|

&&

||

?:

=
```

Always use parentheses if unsure.

---

# Common Mistakes

## Comparing Strings

Wrong

```java
String a = "Java";
String b = "Java";

if(a == b)
```

Correct

```java
if(a.equals(b))
```

---

## Integer Division

```java
7 / 2
```

Output

```
3
```

Not

```
3.5
```

---

## Assignment vs Comparison

Wrong

```java
if(x = 5)
```

Correct

```java
if(x == 5)
```

---

# Production Examples

Spring Security

```java
if(user != null && user.isActive())
```

Second condition executes only if first is true.

---

Validation

```java
return age >= 18 ? "Eligible" : "Not Eligible";
```

---

Pagination

```java
page += 1;
```

---

# Interview Questions

### Difference between

```
&
```

and

```
&&
```

---

### Difference between

```
|
```

and

```
||
```

---

### Explain

```
i++

++i
```

---

### Why does

```java
5 / 2
```

produce

```
2
```

---

### Explain operator precedence.

---

### What is short circuit evaluation?

---

### Difference between == and equals()?

---

# Best Practices

* Use parentheses for readability.
* Prefer `&&` and `||` over `&` and `|` for boolean expressions.
* Use `.equals()` for object comparison.
* Use ternary only for simple expressions.
* Avoid complex nested ternary operators.

---

# Exercises

1. Predict the output:

```java
int a = 10;
int b = a++ + ++a;
System.out.println(b);
```

2. Difference between `==` and `.equals()`.

3. Explain short-circuit evaluation with an example.

4. Why does `5 / 2` return `2`?

5. Write a program using the ternary operator to find the larger of two numbers.

---

# Revision Sheet

* Arithmetic Operators
* Unary Operators
* Relational Operators
* Logical Operators
* Bitwise Operators
* Shift Operators
* Assignment Operators
* Ternary Operator
* instanceof
* Operator Precedence
* Short Circuit Evaluation
* == vs equals()
