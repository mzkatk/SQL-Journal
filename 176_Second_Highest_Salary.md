# Problem Name
LeetCode 176: Second Highest Salary

## Difficulty
Medium

## Link
https://leetcode.com/problems/second-highest-salary/

---

## Problem Statement

Write a solution to find the second highest distinct salary from the `Employee` table.

- If there is no second highest salary, return `NULL`.
- Return `None` in Pandas.

---

## Schema

### Employee

| Column Name | Type |
|-------------|------|
| id          | int  |
| salary      | int  |

- `id` is the primary key.

---

## Sample Input

| id | salary |
|----|--------|
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |

---

## Sample Output

| SecondHighestSalary |
|---------------------|
| 200                 |

---

## Concepts Used

- Aggregate Functions
- Subqueries
- `LIMIT` and `OFFSET`
- Window Functions
- Ranking

---

## Key Insight

- We need the second **distinct** highest salary.
- Duplicate salaries should not affect the result.
- Multiple approaches can solve this problem:
  - Sorting with `LIMIT/OFFSET`
  - Aggregate + Subquery
  - Window Functions

---

## Final Query 1 — LIMIT & OFFSET

```sql
SELECT DISTINCT salary AS SecondHighestSalary
FROM Employee
ORDER BY salary DESC
LIMIT 1 OFFSET 1;
```

---

## Final Query 2 — Aggregate + Subquery

```sql
SELECT MAX(salary) AS SecondHighestSalary
FROM Employee
WHERE salary < (
    SELECT MAX(salary)
    FROM Employee
);
```

---

## Final Query 3 — Window Function

```sql
SELECT salary AS SecondHighestSalary
FROM (
    SELECT salary,
           DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
    FROM Employee
) t
WHERE rnk = 2;
```

---

## Edge Cases

- Duplicate salaries
- Only one employee in table
- NULL should be returned if second highest salary doesn't exist

---

## Complexity Analysis

### Query 1
- Time Complexity: O(n log n)
- Space Complexity: O(1)

### Query 2
- Time Complexity: O(n)
- Space Complexity: O(1)

### Query 3
- Time Complexity: O(n log n)
- Space Complexity: O(n)

---

## What I Learned

- Difference between highest and second highest distinct values
- Using `DENSE_RANK()` for ranking problems
- Solving the same SQL problem using multiple approaches
- Importance of handling duplicates and edge cases