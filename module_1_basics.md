# Module 1 - Java Architecture

> **Goal:** Understand how Java code is compiled and executed.

---

# 1. Source Code

When you write

```java
public class Main {

    public static void main(String[] args) {
        System.out.println("Hello");
    }

}
```

this file is called **source code**.

- Extension: `.java`
- Human readable
- Cannot be executed directly by CPU

---

# 2. Compilation

Java compiler (`javac`) converts source code into bytecode.

```bash
javac Main.java
```

Output:

```
Main.class
```

---

# 3. Bytecode

Bytecode is platform-independent.

```
Main.java
      │
      ▼
   javac
      │
      ▼
Main.class
```

---

# Interview Question

### Q. Why does Java use bytecode?

**Answer**

Java uses bytecode so the same compiled program can run on any operating system that has a compatible JVM.