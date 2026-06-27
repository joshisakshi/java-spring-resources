# Module 5 - Methods (`05-Methods.md`)

# Methods in Java

> **Goal:** Understand methods, parameter passing, overloading, varargs, recursion, scope, stack memory, and interview-focused concepts.

---

# Table of Contents

1. What is a Method?
2. Method Syntax
3. Method Components
4. Method Types
5. Parameter Passing
6. Return Types
7. Method Overloading
8. Varargs
9. Recursion
10. Memory (Stack Frames)
11. Interview Questions
12. Best Practices
13. Exercises

---

# 1. What is a Method?

A **method** is a reusable block of code that performs a specific task.

Instead of writing the same logic repeatedly, we place it inside a method and call it whenever needed.

Example

```java
public static void greet() {
    System.out.println("Hello");
}
```

Call

```java
greet();
```

---

# 2. Method Syntax

```java
accessModifier returnType methodName(parameters) {

    // body

}
```

Example

```java
public int add(int a, int b) {
    return a + b;
}
```

---

# 3. Method Components

```java
public int add(int a, int b)
```

| Part         | Meaning         |
| ------------ | --------------- |
| public       | Access Modifier |
| int          | Return Type     |
| add          | Method Name     |
| int a, int b | Parameters      |

---

# 4. Method Types

## No Parameters, No Return

```java
public void greet() {
    System.out.println("Hello");
}
```

---

## Parameters, No Return

```java
public void printSquare(int x) {
    System.out.println(x * x);
}
```

---

## No Parameters, Return Value

```java
public int getAge() {
    return 25;
}
```

---

## Parameters + Return

```java
public int add(int a, int b) {
    return a + b;
}
```

Most common in backend development.

---

# 5. Parameters vs Arguments

Method

```java
add(int a, int b)
```

`a` and `b` are **parameters**.

Call

```java
add(10, 20);
```

`10` and `20` are **arguments**.

---

# 6. Return Type

Every method either returns a value or returns nothing (`void`).

```java
public double calculateTax(double salary) {
    return salary * 0.10;
}
```

Void

```java
public void display() {
    System.out.println("Welcome");
}
```

---

# 7. Pass By Value

**Java is always Pass By Value.**

Primitive Example

```java
public void change(int x) {
    x = 100;
}

int a = 10;
change(a);

System.out.println(a);
```

Output

```
10
```

Reason:

A copy of `a` is passed.

---

## Objects

```java
Employee emp = new Employee();
change(emp);
```

The **reference is copied**, not the object.

Inside the method you can modify the object's fields.

```java
emp.name = "John";
```

But assigning a new object to the parameter does not change the caller's reference.

---

# 8. Method Overloading

Multiple methods with the same name but different parameter lists.

```java
int add(int a, int b)

double add(double a, double b)

int add(int a, int b, int c)
```

Compiler decides which method to call based on arguments.

---

## Invalid Overloading

Changing only the return type is **not** overloading.

Wrong

```java
int add(int a, int b)

double add(int a, int b)
```

Compile-time error.

---

# 9. Varargs

Variable-length arguments.

Syntax

```java
public int sum(int... numbers)
```

Example

```java
public int sum(int... nums) {

    int total = 0;

    for (int num : nums) {
        total += num;
    }

    return total;
}
```

Usage

```java
sum(1);

sum(1, 2);

sum(1, 2, 3, 4, 5);
```

---

# Rules for Varargs

Only one varargs parameter is allowed.

```java
method(int... nums)
```

Varargs must be the **last parameter**.

Correct

```java
method(String name, int... nums)
```

Wrong

```java
method(int... nums, String name)
```

---

# 10. Method Recursion

A method calling itself.

Example

```java
public int factorial(int n) {

    if (n == 1)
        return 1;

    return n * factorial(n - 1);
}
```

Execution

```
factorial(4)

↓

4 * factorial(3)

↓

3 * factorial(2)

↓

2 * factorial(1)

↓

1
```

Output

```
24
```

---

# Base Case

Every recursive method must have a stopping condition.

Without it

```
StackOverflowError
```

---

# 11. Method Call Stack

Example

```java
main()

↓

login()

↓

authenticate()

↓

database()
```

Each call creates a **stack frame**.

When a method completes, its frame is removed.

---

# Local Variables

Local variables live inside the stack frame.

```java
public void test() {

    int x = 10;

}
```

`x` is destroyed when the method returns.

---

# Static Methods

Belong to the class.

```java
Math.max(10, 20);
```

Can be called without creating an object.

---

# Instance Methods

Belong to an object.

```java
Employee emp = new Employee();

emp.getSalary();
```

---

# Interview Questions

## Q1 Is Java Pass By Reference?

No.

Java is always **Pass By Value**.

---

## Q2 Difference between Parameters and Arguments?

Parameters → Method definition

Arguments → Method call

---

## Q3 Can we overload main()?

Yes.

Only

```java
public static void main(String[] args)
```

is treated as the program entry point.

---

## Q4 Can static methods be overridden?

No.

They are hidden, not overridden.

---

## Q5 Can we overload constructors?

Yes.

Constructor overloading is common.

---

## Q6 Difference between Overloading and Overriding?

| Overloading          | Overriding     |
| -------------------- | -------------- |
| Same class           | Parent & Child |
| Compile Time         | Runtime        |
| Different parameters | Same signature |

---

# Spring Boot Connection

Controller

```java
@GetMapping("/users")
public List<User> getUsers() {
    return service.getUsers();
}
```

Service

```java
public List<User> getUsers() {
    return repository.findAll();
}
```

Repository

```java
public List<User> findAll() {
    ...
}
```

Every layer communicates using methods.

---

# Best Practices

* Method names should describe behavior.
* Keep methods small and focused.
* Prefer returning values over printing inside methods.
* Avoid long parameter lists.
* Use helper methods to reduce duplication.

---

# Common Mistakes

❌ Forgetting `return`.

❌ Infinite recursion.

❌ Confusing parameters with arguments.

❌ Assuming Java is pass by reference.

❌ Creating methods that perform too many responsibilities.

---

# Exercises

1. Write a method to find the maximum of three numbers.
2. Write a recursive factorial method.
3. Write a recursive Fibonacci method.
4. Overload a `print()` method for `int`, `double`, and `String`.
5. Create a varargs method to calculate the average.
6. Explain why Java is pass by value with an example.

---

# Revision Sheet

* Method Syntax
* Parameters vs Arguments
* Return Types
* Pass By Value
* Method Overloading
* Varargs
* Recursion
* Stack Frames
* Static vs Instance Methods
* Interview Questions
