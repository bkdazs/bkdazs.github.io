---
title: "SQL Fundamentals and Patterns"
topic: "SQL"
difficulty: "Medium"
tags: ["sql", "database", "queries", "joins", "window-functions"]
status: "complete"
weight: 1
---

# SQL Fundamentals and Interview Patterns

## 📚 Summary

This comprehensive guide covers SQL concepts commonly tested in FAANG interviews: basic queries, JOINs, aggregations, window functions, and advanced patterns.

---

## 1️⃣ Basic Queries

### SELECT, WHERE, ORDER BY

```sql
-- Basic selection with conditions
SELECT column1, column2
FROM table_name
WHERE condition1 AND condition2
ORDER BY column1 DESC, column2 ASC
LIMIT 10 OFFSET 5;

-- Common operators
WHERE age >= 18 AND age <= 65
WHERE name LIKE 'John%'          -- Starts with John
WHERE name LIKE '%son'           -- Ends with son
WHERE name LIKE '%oh%'           -- Contains oh
WHERE status IN ('active', 'pending')
WHERE email IS NOT NULL
WHERE salary BETWEEN 50000 AND 100000
```

### DISTINCT and Aliases

```sql
-- Remove duplicates
SELECT DISTINCT department FROM employees;

-- Column and table aliases
SELECT 
    e.first_name AS fname,
    e.salary * 12 AS annual_salary
FROM employees e;
```

### CASE Expressions

```sql
SELECT 
    name,
    salary,
    CASE 
        WHEN salary >= 100000 THEN 'High'
        WHEN salary >= 50000 THEN 'Medium'
        ELSE 'Low'
    END AS salary_band
FROM employees;
```

---

## 2️⃣ JOINs

### JOIN Types

```sql
-- INNER JOIN: Only matching rows
SELECT e.name, d.department_name
FROM employees e
INNER JOIN departments d ON e.dept_id = d.id;

-- LEFT JOIN: All from left, matching from right (NULL if no match)
SELECT e.name, d.department_name
FROM employees e
LEFT JOIN departments d ON e.dept_id = d.id;

-- RIGHT JOIN: All from right, matching from left
SELECT e.name, d.department_name
FROM employees e
RIGHT JOIN departments d ON e.dept_id = d.id;

-- FULL OUTER JOIN: All rows from both tables
SELECT e.name, d.department_name
FROM employees e
FULL OUTER JOIN departments d ON e.dept_id = d.id;

-- CROSS JOIN: Cartesian product
SELECT e.name, p.project_name
FROM employees e
CROSS JOIN projects p;
```

### Self-Join

```sql
-- Find employees and their managers
SELECT 
    e.name AS employee,
    m.name AS manager
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.id;

-- Find pairs of employees in same department
SELECT 
    e1.name AS employee1,
    e2.name AS employee2,
    e1.department
FROM employees e1
JOIN employees e2 
    ON e1.department = e2.department 
    AND e1.id < e2.id;
```

### Multiple JOINs

```sql
SELECT 
    e.name,
    d.department_name,
    p.project_name
FROM employees e
JOIN departments d ON e.dept_id = d.id
JOIN employee_projects ep ON e.id = ep.employee_id
JOIN projects p ON ep.project_id = p.id;
```

---

## 3️⃣ Aggregations

### GROUP BY and Aggregate Functions

```sql
-- Basic aggregations
SELECT 
    department,
    COUNT(*) AS employee_count,
    AVG(salary) AS avg_salary,
    MAX(salary) AS max_salary,
    MIN(salary) AS min_salary,
    SUM(salary) AS total_salary
FROM employees
GROUP BY department;

-- HAVING filters groups (after aggregation)
SELECT department, AVG(salary) AS avg_salary
FROM employees
GROUP BY department
HAVING AVG(salary) > 50000;

-- Count distinct values
SELECT 
    department,
    COUNT(DISTINCT manager_id) AS num_managers
FROM employees
GROUP BY department;
```

### Grouping with Multiple Columns

```sql
SELECT 
    department,
    job_title,
    COUNT(*) AS count,
    AVG(salary) AS avg_salary
FROM employees
GROUP BY department, job_title
ORDER BY department, avg_salary DESC;
```

---

## 4️⃣ Window Functions

### ROW_NUMBER, RANK, DENSE_RANK

```sql
-- ROW_NUMBER: Unique sequential numbers
-- RANK: Same rank for ties, gaps after
-- DENSE_RANK: Same rank for ties, no gaps

SELECT 
    name,
    department,
    salary,
    ROW_NUMBER() OVER (ORDER BY salary DESC) AS row_num,
    RANK() OVER (ORDER BY salary DESC) AS rank,
    DENSE_RANK() OVER (ORDER BY salary DESC) AS dense_rank
FROM employees;

-- Example output:
-- name    | salary | row_num | rank | dense_rank
-- Alice   | 100000 |    1    |  1   |     1
-- Bob     | 100000 |    2    |  1   |     1
-- Charlie |  90000 |    3    |  3   |     2
```

### PARTITION BY

```sql
-- Rank within each department
SELECT 
    name,
    department,
    salary,
    RANK() OVER (
        PARTITION BY department 
        ORDER BY salary DESC
    ) AS dept_rank
FROM employees;

-- Get top N per group
WITH ranked AS (
    SELECT 
        *,
        ROW_NUMBER() OVER (
            PARTITION BY department 
            ORDER BY salary DESC
        ) AS rn
    FROM employees
)
SELECT * FROM ranked WHERE rn <= 3;
```

### LAG, LEAD

```sql
-- Compare with previous/next row
SELECT 
    date,
    revenue,
    LAG(revenue, 1) OVER (ORDER BY date) AS prev_day,
    LEAD(revenue, 1) OVER (ORDER BY date) AS next_day,
    revenue - LAG(revenue, 1) OVER (ORDER BY date) AS daily_change
FROM daily_sales;

-- Find consecutive days
SELECT *
FROM daily_sales
WHERE revenue > LAG(revenue) OVER (ORDER BY date);
```

### Running Totals and Moving Averages

```sql
-- Running total
SELECT 
    date,
    revenue,
    SUM(revenue) OVER (ORDER BY date) AS running_total
FROM daily_sales;

-- Running total within partition
SELECT 
    department,
    date,
    revenue,
    SUM(revenue) OVER (
        PARTITION BY department 
        ORDER BY date
    ) AS dept_running_total
FROM sales;

-- Moving average (last 7 days)
SELECT 
    date,
    revenue,
    AVG(revenue) OVER (
        ORDER BY date 
        ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
    ) AS moving_avg_7d
FROM daily_sales;
```

### FIRST_VALUE, LAST_VALUE, NTH_VALUE

```sql
SELECT 
    name,
    department,
    salary,
    FIRST_VALUE(name) OVER (
        PARTITION BY department 
        ORDER BY salary DESC
    ) AS highest_paid_in_dept,
    LAST_VALUE(name) OVER (
        PARTITION BY department 
        ORDER BY salary DESC
        ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
    ) AS lowest_paid_in_dept
FROM employees;
```

---

## 5️⃣ Subqueries and CTEs

### Subqueries

```sql
-- Subquery in WHERE
SELECT * FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);

-- Subquery in FROM (derived table)
SELECT dept, avg_sal
FROM (
    SELECT department AS dept, AVG(salary) AS avg_sal
    FROM employees
    GROUP BY department
) AS dept_salaries
WHERE avg_sal > 50000;

-- Correlated subquery
SELECT *
FROM employees e1
WHERE salary > (
    SELECT AVG(salary) 
    FROM employees e2 
    WHERE e2.department = e1.department
);
```

### Common Table Expressions (CTEs)

```sql
-- Simple CTE
WITH high_earners AS (
    SELECT * FROM employees WHERE salary > 100000
)
SELECT department, COUNT(*) 
FROM high_earners
GROUP BY department;

-- Multiple CTEs
WITH 
dept_stats AS (
    SELECT 
        department,
        AVG(salary) AS avg_salary
    FROM employees
    GROUP BY department
),
above_avg AS (
    SELECT e.*
    FROM employees e
    JOIN dept_stats d ON e.department = d.department
    WHERE e.salary > d.avg_salary
)
SELECT * FROM above_avg;
```

### Recursive CTEs

```sql
-- Hierarchical data (org chart)
WITH RECURSIVE org_chart AS (
    -- Base case: top-level employees (no manager)
    SELECT id, name, manager_id, 1 AS level
    FROM employees
    WHERE manager_id IS NULL
    
    UNION ALL
    
    -- Recursive case
    SELECT e.id, e.name, e.manager_id, oc.level + 1
    FROM employees e
    JOIN org_chart oc ON e.manager_id = oc.id
)
SELECT * FROM org_chart ORDER BY level, name;

-- Generate series (numbers 1-10)
WITH RECURSIVE numbers AS (
    SELECT 1 AS n
    UNION ALL
    SELECT n + 1 FROM numbers WHERE n < 10
)
SELECT * FROM numbers;
```

---

## 6️⃣ Common Interview Patterns

### Pattern 1: Second Highest Salary

```sql
-- Method 1: Subquery with MAX
SELECT MAX(salary) AS second_highest
FROM employees
WHERE salary < (SELECT MAX(salary) FROM employees);

-- Method 2: DENSE_RANK
SELECT salary AS second_highest
FROM (
    SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
    FROM employees
) ranked
WHERE rnk = 2;

-- Method 3: LIMIT OFFSET
SELECT DISTINCT salary
FROM employees
ORDER BY salary DESC
LIMIT 1 OFFSET 1;
```

### Pattern 2: Nth Highest Salary

```sql
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
    RETURN (
        SELECT DISTINCT salary
        FROM (
            SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
            FROM employees
        ) ranked
        WHERE rnk = N
    );
END;
```

### Pattern 3: Consecutive Days/Numbers

```sql
-- Find users who logged in for 3+ consecutive days
WITH numbered AS (
    SELECT 
        user_id,
        login_date,
        login_date - INTERVAL ROW_NUMBER() OVER (
            PARTITION BY user_id ORDER BY login_date
        ) DAY AS grp
    FROM logins
),
streaks AS (
    SELECT user_id, grp, COUNT(*) AS streak_length
    FROM numbered
    GROUP BY user_id, grp
)
SELECT DISTINCT user_id
FROM streaks
WHERE streak_length >= 3;
```

### Pattern 4: Department Top Earners

```sql
-- Employees who earn the most in their department
SELECT department, name, salary
FROM (
    SELECT 
        *,
        RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS rnk
    FROM employees
) ranked
WHERE rnk = 1;
```

### Pattern 5: Year-over-Year Comparison

```sql
WITH yearly AS (
    SELECT 
        YEAR(order_date) AS year,
        SUM(amount) AS revenue
    FROM orders
    GROUP BY YEAR(order_date)
)
SELECT 
    curr.year,
    curr.revenue,
    prev.revenue AS prev_year_revenue,
    ROUND((curr.revenue - prev.revenue) / prev.revenue * 100, 2) AS yoy_growth
FROM yearly curr
LEFT JOIN yearly prev ON curr.year = prev.year + 1;
```

### Pattern 6: Gaps and Islands

```sql
-- Find gaps in sequence
WITH all_ids AS (
    SELECT id, LEAD(id) OVER (ORDER BY id) AS next_id
    FROM users
)
SELECT id + 1 AS gap_start, next_id - 1 AS gap_end
FROM all_ids
WHERE next_id - id > 1;
```

### Pattern 7: Cumulative Sum with Reset

```sql
-- Reset running total on certain condition
SELECT 
    date,
    amount,
    SUM(CASE WHEN reset_flag THEN 0 ELSE amount END) OVER (
        ORDER BY date 
        ROWS UNBOUNDED PRECEDING
    ) AS running_total
FROM transactions;
```

---

## 7️⃣ Performance Tips

### Indexing

```sql
-- Create index for frequently filtered columns
CREATE INDEX idx_emp_dept ON employees(department);
CREATE INDEX idx_emp_salary ON employees(salary);

-- Composite index for multi-column filters
CREATE INDEX idx_emp_dept_salary ON employees(department, salary);
```

### Query Optimization

```sql
-- Use EXISTS instead of IN for large subqueries
-- Slow
SELECT * FROM employees WHERE dept_id IN (SELECT id FROM departments WHERE active = 1);

-- Faster
SELECT * FROM employees e
WHERE EXISTS (SELECT 1 FROM departments d WHERE d.id = e.dept_id AND d.active = 1);

-- Avoid SELECT * in production
SELECT id, name, salary FROM employees;  -- Better than SELECT *

-- Use LIMIT for pagination
SELECT * FROM employees ORDER BY id LIMIT 20 OFFSET 40;
```

---

## 📚 Practice Problems

### Easy
| # | Problem | Key Concept |
|---|---------|-------------|
| 1 | [LC 175: Combine Two Tables](https://leetcode.com/problems/combine-two-tables/) | LEFT JOIN |
| 2 | [LC 181: Employees Earning More Than Managers](https://leetcode.com/problems/employees-earning-more-than-their-managers/) | Self-join |
| 3 | [LC 182: Duplicate Emails](https://leetcode.com/problems/duplicate-emails/) | GROUP BY HAVING |
| 4 | [LC 183: Customers Who Never Order](https://leetcode.com/problems/customers-who-never-order/) | LEFT JOIN NULL |
| 5 | [LC 196: Delete Duplicate Emails](https://leetcode.com/problems/delete-duplicate-emails/) | Self-join DELETE |

### Medium
| # | Problem | Key Concept |
|---|---------|-------------|
| 6 | [LC 176: Second Highest Salary](https://leetcode.com/problems/second-highest-salary/) | Subquery/LIMIT |
| 7 | [LC 177: Nth Highest Salary](https://leetcode.com/problems/nth-highest-salary/) | DENSE_RANK |
| 8 | [LC 178: Rank Scores](https://leetcode.com/problems/rank-scores/) | DENSE_RANK |
| 9 | [LC 180: Consecutive Numbers](https://leetcode.com/problems/consecutive-numbers/) | Self-join/LAG |
| 10 | [LC 184: Department Highest Salary](https://leetcode.com/problems/department-highest-salary/) | Window + filter |
| 11 | [LC 550: Game Play Analysis IV](https://leetcode.com/problems/game-play-analysis-iv/) | LAG/LEAD |
| 12 | [LC 570: Managers with 5+ Reports](https://leetcode.com/problems/managers-with-at-least-5-direct-reports/) | GROUP BY COUNT |
| 13 | [LC 608: Tree Node](https://leetcode.com/problems/tree-node/) | CASE + subquery |
| 14 | [LC 1158: Market Analysis I](https://leetcode.com/problems/market-analysis-i/) | LEFT JOIN + agg |
| 15 | [LC 1393: Capital Gain/Loss](https://leetcode.com/problems/capital-gainloss/) | Conditional SUM |

### Hard
| # | Problem | Key Concept |
|---|---------|-------------|
| 16 | [LC 185: Department Top 3 Salaries](https://leetcode.com/problems/department-top-three-salaries/) | DENSE_RANK ≤ 3 |
| 17 | [LC 262: Trips and Users](https://leetcode.com/problems/trips-and-users/) | Complex joins |
| 18 | [LC 569: Median Employee Salary](https://leetcode.com/problems/median-employee-salary/) | Window + median |
| 19 | [LC 579: Cumulative Salary](https://leetcode.com/problems/find-cumulative-salary-of-an-employee/) | Rolling sum |
| 20 | [LC 601: Human Traffic of Stadium](https://leetcode.com/problems/human-traffic-of-stadium/) | Consecutive rows |
| 21 | [LC 1097: Game Play Analysis V](https://leetcode.com/problems/game-play-analysis-v/) | Retention calc |
| 22 | [LC 1127: User Purchase Platform](https://leetcode.com/problems/user-purchase-platform/) | CASE + GROUP BY |
| 23 | [LC 1225: Report Contiguous Dates](https://leetcode.com/problems/report-contiguous-dates/) | Gaps and islands |
| 24 | [LC 1336: Number of Transactions Per Visit](https://leetcode.com/problems/number-of-transactions-per-visit/) | Complex agg |
| 25 | [LC 1194: Tournament Winners](https://leetcode.com/problems/tournament-winners/) | Multi-join rank |

---

## 🔑 Key Takeaways

1. **JOINs**: Know all types and when to use each
2. **Window Functions**: Master RANK, ROW_NUMBER, LAG/LEAD
3. **CTEs**: Use for readability and complex queries
4. **Subqueries**: Correlated vs non-correlated
5. **Practice gaps/islands and consecutive patterns**

---

*Last Updated: 2024*
