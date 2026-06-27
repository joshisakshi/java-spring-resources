# Module 4 - Control Flow (`04-Control-Flow.md`)

# Control Flow in Java

> **Goal:** Learn how Java controls program execution using decision-making statements, loops, jump statements, and enhanced for loops.

---

# Table of Contents

1. Introduction
2. if
3. if-else
4. else-if Ladder
5. Nested if
6. switch
7. for Loop
8. while Loop
9. do-while Loop
10. Enhanced for Loop
11. break
12. continue
13. return
14. Interview Questions
15. Best Practices
16. Exercises

---

# What is Control Flow?

Control Flow determines **which statement executes next**.

```text
Start
   ↓
Decision
   ↓
Loop
   ↓
End
```

---

# 1. if Statement

Executes a block only if the condition is true.

```java
int age = 20;

if(age >= 18){
    System.out.println("Eligible");
}
```

---

# 2. if-else

```java
if(age >= 18){
    System.out.println("Adult");
}else{
    System.out.println("Minor");
}
```

---

# 3. else-if Ladder

```java
int marks = 82;

if(marks >= 90){
    System.out.println("A");
}
else if(marks >= 75){
    System.out.println("B");
}
else if(marks >= 60){
    System.out.println("C");
}
else{
    System.out.println("Fail");
}
```

Conditions are checked from top to bottom.

---

# 4. Nested if

```java
if(age >= 18){

    if(hasLicense){
        System.out.println("Can Drive");
    }

}
```

---

# 5. switch Statement

Better than multiple else-if blocks for fixed values.

```java
int day = 2;

switch(day){

case 1:
    System.out.println("Monday");
    break;

case 2:
    System.out.println("Tuesday");
    break;

default:
    System.out.println("Invalid");

}
```

---

## Switch Expression (Java 14+)

```java
String result = switch(day){

case 1 -> "Monday";

case 2 -> "Tuesday";

default -> "Invalid";

};
```

---

# 6. for Loop

Used when number of iterations is known.

```java
for(int i=1;i<=5;i++){

    System.out.println(i);

}
```

Structure

```text
Initialization

↓

Condition

↓

Body

↓

Increment

↓

Repeat
```

---

# 7. while Loop

Condition checked before execution.

```java
int i=1;

while(i<=5){

    System.out.println(i);

    i++;

}
```

---

# 8. do-while Loop

Runs at least once.

```java
int i=10;

do{

    System.out.println(i);

}while(i<5);
```

Output

```text
10
```

---

# 9. Enhanced for Loop

Used for arrays and collections.

```java
int[] nums={1,2,3,4};

for(int num:nums){

    System.out.println(num);

}
```

Collection example

```java
for(String name:names){

    System.out.println(name);

}
```

---

# 10. break

Terminates the loop immediately.

```java
for(int i=1;i<=10;i++){

    if(i==5)
        break;

    System.out.println(i);

}
```

Output

```text
1
2
3
4
```

---

# 11. continue

Skips the current iteration.

```java
for(int i=1;i<=5;i++){

    if(i==3)
        continue;

    System.out.println(i);

}
```

Output

```text
1
2
4
5
```

---

# 12. return

Immediately exits a method.

```java
public int square(int n){

    return n*n;

}
```

---

# Nested Loops

```java
for(int i=1;i<=3;i++){

    for(int j=1;j<=3;j++){

        System.out.print("* ");

    }

    System.out.println();

}
```

Output

```text
* * *
* * *
* * *
```

---

# Infinite Loop

```java
while(true){

}
```

Exit using

```java
break;
```

---

# Interview Questions

### Difference between while and do-while?

| while               | do-while               |
| ------------------- | ---------------------- |
| Condition first     | Body first             |
| May execute 0 times | Executes at least once |

---

### Difference between break and continue?

| break     | continue                |
| --------- | ----------------------- |
| Ends loop | Skips current iteration |

---

### When should switch be preferred?

When comparing one variable against multiple constant values.

---

### Can switch work with String?

Yes.

Supported since Java 7.

---

### Enhanced for limitations

Cannot

* access index
* modify collection while iterating
* iterate backwards

---

# Spring Boot Connection

Loop through entities

```java
for(User user : users){

    System.out.println(user.getName());

}
```

Validation

```java
if(user == null){

    throw new RuntimeException();

}
```

Switch

```java
switch(role){

case "ADMIN" -> ...

case "USER" -> ...

}
```

---

# Best Practices

* Prefer enhanced for loops for collections.
* Prefer switch expressions for readability.
* Avoid deeply nested if statements.
* Use break only when necessary.
* Keep loop bodies simple.

---

# Exercises

1. Print numbers 1–100.
2. Print even numbers.
3. Reverse a number.
4. Find factorial using a loop.
5. Print multiplication table.
6. Print star patterns.
7. Sum elements of an array using enhanced for.
8. Implement a menu using switch.

---

# Revision Sheet

* if
* if-else
* else-if
* Nested if
* switch
* switch expression
* for
* while
* do-while
* Enhanced for
* break
* continue
* return
* Infinite loop
* Nested loops
