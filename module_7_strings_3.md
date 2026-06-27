# Module 7 - Strings (Part 3)

# String Methods, Internals & Interview Problems (`07-Strings-Part3-String-Methods-and-Interview-Problems.md`)

> **Goal:** Master Java String APIs, understand their internal behavior and complexity, and solve the most common String interview questions asked in Java backend interviews.

---

# Table of Contents

1. Common String Methods
2. Time Complexity Cheat Sheet
3. String Comparison
4. Searching Methods
5. Modification Methods
6. Whitespace Methods
7. Conversion Methods
8. Regular Expressions
9. Formatting
10. Interview Coding Problems
11. Spring Boot Usage
12. Best Practices
13. Interview Questions
14. Revision Sheet

---

# 1. Frequently Used String Methods

---

## length()

Returns number of characters.

```java
String s = "Spring";

System.out.println(s.length());
```

Output

```text
6
```

Time Complexity

```text
O(1)
```

Internally, String stores its length, so it doesn't count characters every time.

---

## isEmpty()

Checks if length is zero.

```java
String s = "";

System.out.println(s.isEmpty());
```

Output

```text
true
```

Equivalent to

```java
s.length() == 0
```

---

## isBlank() (Java 11)

Checks whether a string contains only whitespace.

```java
"   ".isBlank()
```

Returns

```text
true
```

Difference

```text
""      -> empty & blank

"   "   -> blank but NOT empty
```

---

## charAt()

Returns character at given index.

```java
String s = "Java";

System.out.println(s.charAt(2));
```

Output

```text
v
```

Complexity

```text
O(1)
```

---

## substring()

Extracts part of a string.

```java
String s = "SpringBoot";

System.out.println(s.substring(6));
```

Output

```text
Boot
```

Another form

```java
s.substring(0,6);
```

Output

```text
Spring
```

Complexity

```text
O(n)
```

---

# 2. Searching Methods

---

## contains()

```java
String s = "Spring Boot";

System.out.println(s.contains("Boot"));
```

Output

```text
true
```

---

## startsWith()

```java
s.startsWith("Spr");
```

---

## endsWith()

```java
s.endsWith("Boot");
```

---

## indexOf()

Returns first occurrence.

```java
String s = "banana";

System.out.println(s.indexOf('a'));
```

Output

```text
1
```

---

## lastIndexOf()

Returns last occurrence.

```java
banana

↓

5
```

---

# 3. Comparison Methods

---

## equals()

Compares content.

```java
a.equals(b)
```

Preferred for string comparison.

---

## equalsIgnoreCase()

```java
JAVA

java
```

Returns

```text
true
```

---

## compareTo()

Lexicographical comparison.

```java
Apple.compareTo(Banana)
```

Returns

Negative number.

If equal

```text
0
```

If greater

Positive number.

Used for sorting.

---

## compareToIgnoreCase()

Ignores letter case.

---

# 4. Modification Methods

Remember:

**All return a NEW String.**

---

## concat()

```java
String s = "Java";

s.concat(" Spring");
```

---

## replace()

```java
Java

↓

replace("J","K")
```

Output

```text
Kava
```

---

## replaceAll()

Uses Regular Expressions.

```java
s.replaceAll("\\d","");
```

Removes digits.

---

## replaceFirst()

Only first match.

---

## toUpperCase()

---

## toLowerCase()

---

## repeat()

(Java 11)

```java
"*".repeat(5)
```

Output

```text
*****
```

---

# 5. Splitting

```java
String csv = "A,B,C";

String[] arr = csv.split(",");
```

Output

```text
A

B

C
```

Very common while parsing CSV or configuration values.

---

# 6. Joining

```java
String.join("-", "2025","06","27");
```

Output

```text
2025-06-27
```

---

# 7. Trimming

## trim()

Removes leading and trailing spaces.

```java
" Java ".trim()
```

---

## strip()

Java 11.

Unicode-aware version of trim().

Preferred in modern applications.

---

# 8. Conversion Methods

---

## valueOf()

Converts primitive to String.

```java
String.valueOf(100)
```

---

## toCharArray()

```java
char[] letters = s.toCharArray();
```

Very common in interview questions.

---

## getBytes()

Converts String to byte array.

Useful in networking and file handling.

---

# 9. Formatting

```java
String.format(
    "Hello %s",
    "Sakshi"
);
```

Output

```text
Hello Sakshi
```

---

# 10. Regular Expressions

Very common in backend development.

Remove digits

```java
replaceAll("\\d","")
```

Remove spaces

```java
replaceAll("\\s+","")
```

Only alphabets

```java
replaceAll("[^A-Za-z]","")
```

---

# Time Complexity Cheat Sheet

| Method      | Complexity        |
| ----------- | ----------------- |
| length      | O(1)              |
| charAt      | O(1)              |
| equals      | O(n)              |
| compareTo   | O(n)              |
| substring   | O(n)              |
| contains    | O(n)              |
| replace     | O(n)              |
| split       | O(n) + regex cost |
| toUpperCase | O(n)              |
| toLowerCase | O(n)              |
| trim        | O(n)              |

---

# Common Interview Coding Problems

---

## Reverse String

Approaches

* Loop
* StringBuilder
* Two pointers

---

## Palindrome

Example

```text
madam
```

---

## Count Characters

```text
banana

↓

a = 3
```

Use

```java
HashMap<Character,Integer>
```

---

## First Non-Repeating Character

Example

```text
aabbcd

↓

c
```

---

## Duplicate Characters

```text
programming
```

Output duplicates.

---

## Remove Duplicate Characters

```text
banana

↓

ban
```

---

## Reverse Words

```text
Java Spring Boot
```

↓

```text
Boot Spring Java
```

---

## Anagram

```text
listen

silent
```

Solutions

* Sorting
* Frequency array

---

## Count Words

---

## Remove Whitespaces

---

## Check Rotation

```text
ABCD

CDAB
```

---

## Longest Common Prefix

```text
flower

flow

flight
```

↓

```text
fl
```

---

## String Compression

```text
aaabb

↓

a3b2
```

---

## Character Frequency

---

## Remove Special Characters

---

## Check Only Digits

---

## Count Vowels

---

## Longest Substring Without Repeating Characters

Sliding Window.

Important interview problem.

---

# Spring Boot Usage

Strings appear in

* REST endpoints
* Request parameters
* DTOs
* JSON serialization
* SQL queries
* Logging
* Exception messages
* Configuration
* HTTP headers
* JWT tokens
* File paths

Understanding String APIs is essential for almost every backend task.

---

# Common Mistakes

❌ Using `==` instead of `equals()`.

❌ Forgetting Strings are immutable.

❌ Using `replaceAll()` when `replace()` is sufficient (regex is slower).

❌ Calling `split()` repeatedly in loops.

❌ Ignoring `null` checks before calling String methods.

---

# Interview Questions

### Why are Strings immutable?

---

### Difference between `trim()` and `strip()`?

---

### Difference between `replace()` and `replaceAll()`?

---

### Difference between `==` and `equals()`?

---

### Difference between `compareTo()` and `equals()`?

---

### Why is `StringBuilder` faster than String?

---

### Difference between `StringBuilder` and `StringBuffer`?

---

### Why is String a good HashMap key?

---

### Explain the String Pool.

---

### How is String concatenation optimized by the compiler?

---

### What is the complexity of `contains()`?

---

### Why does `substring()` create a new String?

---

### Explain `intern()`.

---

### Which String methods use Regular Expressions?

`split()`, `replaceAll()`, `replaceFirst()`.

---

# Best Practices

* Prefer `equals()` over `==`.
* Use `StringBuilder` for repeated concatenation.
* Prefer `replace()` over `replaceAll()` unless regex is required.
* Use `isBlank()` instead of manual whitespace checks (Java 11+).
* Avoid unnecessary String object creation.
* Handle `null` safely (e.g., `"constant".equals(variable)` when appropriate).

---

# Revision Sheet

## Internals

* String Pool
* Heap
* Immutability
* `intern()`
* `final`
* Compiler Optimization

## Mutable Strings

* StringBuilder
* StringBuffer
* Capacity
* Thread Safety

## APIs

* length()
* isEmpty()
* isBlank()
* charAt()
* substring()
* contains()
* startsWith()
* endsWith()
* indexOf()
* lastIndexOf()
* equals()
* compareTo()
* replace()
* replaceAll()
* split()
* trim()
* strip()
* repeat()
* join()
* format()
* valueOf()
* toCharArray()

## Interview Problems

* Reverse String
* Palindrome
* Anagram
* Character Frequency
* Duplicate Characters
* Remove Duplicates
* Reverse Words
* Longest Common Prefix
* String Compression
* Longest Substring Without Repeating Characters
* Check Rotation
* Remove Whitespaces
* Count Vowels
* Count Words
* First Non-Repeating Character

## Spring Boot Connection

* REST APIs
* JSON
* DTOs
* Logging
* SQL
* Configuration
* JWT
* HTTP Headers
* File Handling
