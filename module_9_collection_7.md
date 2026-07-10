# Module 9 - Collections (Part 7)

# Comparable & Comparator Deep Dive

> **Goal:** Master sorting in Java by understanding Comparable and Comparator, their internal workings, natural vs custom ordering, Java 8 enhancements, and how they are used in real-world Spring Boot applications.

---

# Table of Contents

1. Why Do We Need Sorting?
2. Comparable Interface
3. Internal Working of Comparable
4. Comparator Interface
5. Internal Working of Comparator
6. Comparable vs Comparator
7. Natural Ordering
8. Custom Ordering
9. Multiple Field Sorting
10. Java 8 Comparator Enhancements
11. TreeSet & TreeMap Ordering
12. Collections.sort() vs List.sort()
13. TimSort
14. Spring Boot Usage
15. Best Practices
16. Common Mistakes
17. Interview Questions
18. Exercises
19. Revision Sheet

---

# 1. Why Do We Need Sorting?

Almost every application needs sorting.

Examples:

- Sort employees by salary
- Sort products by price
- Sort students by marks
- Sort orders by creation date
- Sort users alphabetically

Suppose we have:

```text
Charlie
Alice
Bob
```

Desired output:

```text
Alice
Bob
Charlie
```

Java provides two mechanisms for sorting:

1. Comparable
2. Comparator

---

# 2. Comparable Interface

Package:

```java
java.lang.Comparable<T>
```

Comparable defines the **natural ordering** of objects.

Method:

```java
int compareTo(T obj);
```

When a class implements Comparable, it decides how its own objects should be sorted.

---

## Example

```java
class Student implements Comparable<Student> {

    int marks;

    Student(int marks) {
        this.marks = marks;
    }

    @Override
    public int compareTo(Student other) {
        return this.marks - other.marks;
    }
}
```

Usage

```java
List<Student> students = new ArrayList<>();

students.add(new Student(90));
students.add(new Student(70));
students.add(new Student(80));

Collections.sort(students);
```

Output

```text
70
80
90
```

---

# compareTo() Return Values

```text
this.compareTo(other)

< 0   → Current object comes before other

= 0   → Both objects are equal

> 0   → Current object comes after other
```

Example

```java
10.compareTo(20)
```

Result

```text
-1
```

Meaning:

```text
10

↓

20
```

---

# 3. Internal Working of Comparable

When we call:

```java
Collections.sort(list);
```

Execution flow:

```text
Collections.sort()

↓

List.sort()

↓

TimSort

↓

compareTo()

↓

Determine Order

↓

Sorted List
```

Notice that **TimSort never knows how to compare Student objects**.

Instead, it repeatedly calls:

```java
student1.compareTo(student2);
```

This makes Comparable the object's built-in comparison logic.

---

# Example Execution

Suppose:

```text
90

70

80
```

TimSort compares:

```text
90 vs 70

↓

compareTo()

↓

Positive

↓

Swap
```

Then

```text
90 vs 80

↓

compareTo()

↓

Positive

↓

Swap
```

Finally

```text
70

80

90
```

---

# 4. Comparator Interface

Package:

```java
java.util.Comparator<T>
```

Method:

```java
int compare(T o1, T o2);
```

Comparator defines an **external comparison strategy**.

Unlike Comparable, the class itself does not need to change.

---

## Example

```java
class Student {

    int marks;

    Student(int marks) {
        this.marks = marks;
    }
}
```

Comparator

```java
class MarksComparator
        implements Comparator<Student> {

    @Override
    public int compare(Student s1,
                       Student s2) {

        return s1.marks - s2.marks;
    }
}
```

Usage

```java
Collections.sort(students,
                 new MarksComparator());
```

---

# Why Comparator?

Suppose Employee has:

- Name
- Salary
- Age

Today you need:

```text
Sort by Salary
```

Tomorrow:

```text
Sort by Age
```

Next week:

```text
Sort by Name
```

Changing compareTo() every time is impossible.

Comparator allows multiple sorting strategies.

---

# 5. Internal Working of Comparator

Execution

```text
Collections.sort(list, comparator)

↓

TimSort

↓

Comparator.compare()

↓

Return

-1

0

1

↓

Sorting Completed
```

Instead of calling:

```text
compareTo()
```

TimSort now repeatedly calls:

```java
compare(o1, o2)
```

The Comparator decides the ordering.

---

# Comparable vs Comparator

| Comparable | Comparator |
|------------|------------|
| Package: java.lang | Package: java.util |
| compareTo() | compare() |
| Natural ordering | Custom ordering |
| One sorting logic | Multiple sorting logics |
| Class modified | External class/lambda |
| Used automatically | Passed explicitly |

---

# Natural Ordering

Natural ordering means:

> "How should objects normally be sorted?"

Examples

String

```text
Alphabetical
```

Integer

```text
Ascending
```

LocalDate

```text
Chronological
```

These classes already implement Comparable.

Example

```java
Collections.sort(names);
```

Output

```text
Alice

Bob

Charlie
```

---

# Custom Ordering

Need descending marks?

```java
Comparator<Student> desc =
    (a, b) -> b.marks - a.marks;

Collections.sort(students, desc);
```

Output

```text
95

90

80

70
```

---

# Multiple Field Sorting

Suppose Employee has:

```text
Name

Salary

Age
```

Requirement

1. Salary
2. Age
3. Name

Comparator handles this easily.

```java
Comparator<Employee> comparator =
    Comparator.comparing(Employee::getSalary)
              .thenComparing(Employee::getAge)
              .thenComparing(Employee::getName);
```

This is much cleaner than writing nested if-else statements.

---

# Java 8 Comparator Enhancements

Instead of

```java
Collections.sort(list,
    new Comparator<Employee>() {

        @Override
        public int compare(Employee a,
                           Employee b) {

            return a.getSalary() - b.getSalary();

        }

});
```

Use

```java
list.sort(
    Comparator.comparing(Employee::getSalary)
);
```

Cleaner, shorter, and easier to maintain.

---

# Useful Comparator Methods

Ascending

```java
Comparator.comparing(Employee::getSalary)
```

Descending

```java
Comparator.comparing(Employee::getSalary)
          .reversed()
```

Then Comparing

```java
Comparator.comparing(Employee::getSalary)
          .thenComparing(Employee::getAge)
```

Nulls First

```java
Comparator.nullsFirst(
    Comparator.naturalOrder()
)
```

Nulls Last

```java
Comparator.nullsLast(
    Comparator.naturalOrder()
)
```

Reverse Order

```java
Comparator.reverseOrder()
```

Natural Order

```java
Comparator.naturalOrder()
```

---

# TreeSet & TreeMap Ordering

TreeSet and TreeMap always maintain sorted order.

Default

```java
TreeSet<Integer> set =
    new TreeSet<>();
```

Ascending

```text
10

20

30
```

Custom

```java
TreeSet<Integer> set =
    new TreeSet<>(Comparator.reverseOrder());
```

Output

```text
30

20

10
```

The comparator determines where every element is inserted.

---

# Collections.sort() vs List.sort()

Collections

```java
Collections.sort(list);
```

List

```java
list.sort(comparator);
```

Internally

```text
Collections.sort()

↓

List.sort()

↓

TimSort
```

Since Java 8, `Collections.sort()` delegates to `List.sort()`.

---

# TimSort

Java uses **TimSort** for sorting objects.

TimSort combines:

```text
Merge Sort

+

Insertion Sort
```

Advantages

- Stable sorting
- Very efficient on partially sorted data
- O(n log n) worst case
- O(n) for nearly sorted lists

Primitive arrays (`int[]`, `double[]`) are sorted using a different algorithm (`Dual-Pivot Quicksort`), not TimSort.

---

# Spring Boot Usage

Comparators are used extensively in backend applications.

Examples:

Sorting DTOs

```java
users.sort(
    Comparator.comparing(UserDto::getName)
);
```

Sorting API responses

```java
orders.sort(
    Comparator.comparing(Order::getCreatedAt)
);
```

Priority-based scheduling

Leaderboard ranking

Salary reports

Pagination with custom ordering

Database results (when additional in-memory sorting is required)

---

# Best Practices

✅ Prefer `Comparator.comparing()` over manual subtraction for objects.

---

✅ Use `thenComparing()` for multi-field sorting.

---

✅ Keep `compareTo()` consistent with `equals()` whenever possible.

---

✅ Use method references (`Employee::getSalary`) for readability.

---

✅ Avoid arithmetic subtraction (`a - b`) for large integers because of potential overflow; prefer `Integer.compare(a, b)` or `Comparator.comparingInt()`.

---

# Common Mistakes

❌ Returning incorrect values from `compareTo()`.

---

❌ Violating transitivity in comparisons.

---

❌ Modifying objects while sorting.

---

❌ Forgetting that TreeSet uniqueness depends on the comparator.

---

❌ Using subtraction for comparing `long` values.

---

# Interview Questions

### Difference between Comparable and Comparator?

---

### Why is Comparable in `java.lang` but Comparator in `java.util`?

---

### Which is better for multiple sorting conditions?

---

### What happens if compareTo() always returns 0?

---

### Can TreeSet work without Comparable?

**Answer:** Yes, if a Comparator is provided.

---

### Which sorting algorithm does Java use?

**Answer:** TimSort (for object collections).

---

### Difference between natural ordering and custom ordering?

---

### What is a stable sorting algorithm?

---

### Why is TimSort preferred?

---

### Why should compareTo() be consistent with equals()?

---

# Exercises

1. Implement Comparable for Student (sort by marks).
2. Sort Employee by salary.
3. Sort Employee by age (descending).
4. Sort Employee by salary, then age.
5. Create a TreeSet with reverse ordering.
6. Sort a list using lambda expressions.

---

# Revision Sheet

## Interfaces

- Comparable
- Comparator

## Methods

- compareTo()
- compare()

## Comparator Utilities

- comparing()
- comparingInt()
- thenComparing()
- reversed()
- naturalOrder()
- reverseOrder()
- nullsFirst()
- nullsLast()

## Algorithms

- TimSort (Objects)
- Dual-Pivot Quicksort (Primitive Arrays)

## Interview Focus

- Comparable vs Comparator
- Natural vs Custom Ordering
- TreeSet Ordering
- TimSort
- Stable Sorting
- Java 8 Comparator APIs
- compareTo() Contract
- Comparator Best Practices