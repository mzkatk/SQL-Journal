# Problem Name
Highest Daily Total Order Cost

## Difficulty
Medium

---

## Problem Statement

Find the customers with the highest daily total order cost between `2019-02-01` and `2019-05-01`.

Rules:
- If a customer had multiple orders on the same day, sum their order costs for that day.
- If multiple customers tie for the highest daily total on the same date, return all of them.

Return:
- customer's first name
- total daily order cost
- order date

---

## Tables

### customers

| Column Name  | Type  |
|--------------|------ |
| id | bigint  |       |
| first_name   | text  |
| last_name    | text  |
| city         | text  |
| address      | text  |
| phone_number | text  |

---

### orders

| Column Name      | Type   |
|------------------|--------|
| id | bigint      |        |
| cust_id          | bigint |
| order_date       | date   |
| order_details    | text   |
| total_order_cost | bigint |

---

## Concepts Used

- JOIN
- Aggregation
- GROUP BY
- Window Functions
- DENSE_RANK()
- CTE (Common Table Expression)

---

## Key Insight

The problem requires:
1. Summing all orders per customer per day
2. Finding the highest spender for each day
3. Returning ties as well

`DENSE_RANK()` is ideal because customers with the same highest total receive the same rank.

---

## Final Query

```sql
WITH daily_cost AS (
    SELECT
        c.first_name,
        o.order_date,
        SUM(o.total_order_cost) AS total_cost
    FROM customers c
    JOIN orders o
        ON c.id = o.cust_id
    WHERE o.order_date BETWEEN '2019-02-01' AND '2019-05-01'
    GROUP BY
        c.first_name,
        o.order_date
),

ranked AS (
    SELECT *,
           DENSE_RANK() OVER (
               PARTITION BY order_date
               ORDER BY total_cost DESC
           ) AS rnk
    FROM daily_cost
)

SELECT
    first_name,
    total_cost,
    order_date
FROM ranked
WHERE rnk = 1
ORDER BY order_date;
```

---

## Step-by-Step Explanation

### Step 1 — Calculate Daily Spending

```sql
SUM(o.total_order_cost)
```

If a customer places multiple orders on the same day, their order values are added together.

Example:

| Customer | Date | Orders |
|----------|------|--------|
| Alice | 2019-02-03 | 100 |
| Alice | 2019-02-03 | 200 |

Becomes:

| Customer | Date       | Total |
|----------|------------|-------|
| Alice    | 2019-02-03 | 300   |

---

### Step 2 — Rank Customers Per Day

```sql
DENSE_RANK() OVER (
    PARTITION BY order_date
    ORDER BY total_cost DESC
)
```

For each day:
- Highest spender gets rank `1`
- Ties also receive rank `1`

Example:

| Date       | Customer | Total | Rank |
|------------|----------|-------|------|
| 2019-02-01 | Alice    | 500   | 1    |
| 2019-02-01 | Bob      | 500   | 1    |
| 2019-02-01 | John     | 300   | 2    |

---

### Step 3 — Keep Highest Spenders

```sql
WHERE rnk = 1
```

Returns all top spenders for each date, including ties.

---

## Edge Cases

- Multiple orders by same customer on same date
- Multiple customers tying for highest spending
- Customers with no orders in given range

---

## Complexity Analysis

### Time Complexity
- Aggregation: `O(n)`
- Window Function Ranking: `O(n log n)`

### Space Complexity
- `O(n)` for grouped intermediate result

---

## What I Learned

- How to combine aggregation with window functions
- Using `DENSE_RANK()` to preserve ties
- Importance of grouping before ranking
- Solving "top per group" SQL problems efficiently