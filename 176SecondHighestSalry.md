#Problem Name
LeetCode: Second Highest Salary

## Difficulty
Medium

## Link
https://leetcode.com/problems/second-highest-salary/description/
Table: Employee

## Problem Statement
Write a solution to find the second highest distinct salary from the Employee table. If there is no second highest salary, return null
(return None in Pandas).

## Schema

### Employee
+-------------+------+
| Column Name | Type |
+-------------+------+
| id          | int  |
| salary      | int  |
+-------------+------+


 ## Sample Input

+----+--------+
| id | salary |
+----+--------+
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |
+----+--------+

## Concepts Used
- Aggregate fns
- Subquery
- We can use limit and offset concepts too

## Final Query 1

```sql
SELECT DISTINCT salary AS SecondHighestSalary
FROM Employee
ORDER BY salary DESC
LIMIT 1 OFFSET 1;
```
## Edge Cases
- Duplicate salaries
- Only one employee in table
- NULL should be returned if second highest salary doesn't exist


## Final Query 2

```sql
SELECT MAX(salary) AS SecondHighestSalary
FROM Employee
WHERE salary < (
    SELECT MAX(salary)
    FROM Employee
);
```

## Final Query 3

```sql
SELECT salary AS SecondHighestSalary
FROM (
    SELECT salary,
           DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
    FROM Employee
) t
WHERE rnk = 2;
```