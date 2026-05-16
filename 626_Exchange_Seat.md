# Problem Name
LeetCode 626: Exchange Seats

## Difficulty
Medium

## Link
https://leetcode.com/problems/exchange-seats/

---

## Problem Statement

Write a solution to swap the seat id of every two consecutive students.

- If the number of students is odd, the last student's seat remains unchanged.
- Return the result ordered by `id` in ascending order.

---

## Schema

### Seat

| Column Name | Type    |
|-------------|---------|
| id          | int     |
| student     | varchar |

- `id` is the primary key.
- IDs start from 1 and increment continuously.

---

## Sample Input

| id | student |
|----|---------|
| 1  | Abbot   |
| 2  | Doris   |
| 3  | Emerson |
| 4  | Green   |
| 5  | Jeames  |

---

## Sample Output

| id | student |
|----|---------|
| 1  | Doris   |
| 2  | Abbot   |
| 3  | Green   |
| 4  | Emerson |
| 5  | Jeames  |

---

## Concepts Used

- CASE WHEN
- Conditional Logic
- Modulo Operator (`%`)
- Subquery
- Ordering

---

## Key Insight

- Odd IDs are swapped with the next ID.
- Even IDs are swapped with the previous ID.
- If the last ID is odd and has no next row, it remains unchanged.

---

## Final Query

```sql
SELECT
    CASE
        WHEN id % 2 = 1
             AND id + 1 IN (SELECT id FROM Seat)
        THEN id + 1

        WHEN id % 2 = 0
        THEN id - 1

        ELSE id
    END AS id,
    
    student

FROM Seat

ORDER BY id ASC;
```

---

## Edge Cases

- Odd number of students
- Only one student in table
- Consecutive IDs are guaranteed

---

## Complexity Analysis

- Time Complexity: O(n)
- Space Complexity: O(1)

---

## What I Learned

- Using `CASE WHEN` for row-wise transformations
- How modulo helps identify odd/even rows
- Handling edge cases using conditional checks
```