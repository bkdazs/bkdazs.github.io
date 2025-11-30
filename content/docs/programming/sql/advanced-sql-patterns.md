---
title: "Advanced SQL Interview Patterns"
topic: "SQL"
difficulty: "Hard"
tags: ["sql", "advanced", "window-functions", "recursive-cte", "pivot", "analytics"]
status: "complete"
weight: 2
---

# Advanced SQL Interview Patterns

## 🎯 Tricky Concepts for FAANG Interviews

---

## 1️⃣ Gaps and Islands Problem

### Find Consecutive Login Streaks

```sql
-- Problem: Find users with 3+ consecutive day logins
-- The trick: Subtract row_number from date to create groups

WITH login_groups AS (
    SELECT 
        user_id,
        login_date,
        login_date - INTERVAL ROW_NUMBER() OVER (
            PARTITION BY user_id 
            ORDER BY login_date
        ) DAY AS group_id
    FROM (SELECT DISTINCT user_id, DATE(login_time) AS login_date FROM logins) t
)
SELECT 
    user_id,
    MIN(login_date) AS streak_start,
    MAX(login_date) AS streak_end,
    COUNT(*) AS streak_length
FROM login_groups
GROUP BY user_id, group_id
HAVING COUNT(*) >= 3;
```

### Island Detection (Contiguous Sequences)

```sql
-- Find ranges of continuous IDs
WITH numbered AS (
    SELECT 
        id,
        id - ROW_NUMBER() OVER (ORDER BY id) AS grp
    FROM active_ids
)
SELECT 
    MIN(id) AS range_start,
    MAX(id) AS range_end,
    COUNT(*) AS range_size
FROM numbered
GROUP BY grp
ORDER BY range_start;
```

---

## 2️⃣ Sessionization

### Group Events into Sessions (30 min inactivity)

```sql
WITH time_diff AS (
    SELECT 
        user_id,
        event_time,
        TIMESTAMPDIFF(MINUTE, 
            LAG(event_time) OVER (PARTITION BY user_id ORDER BY event_time),
            event_time
        ) AS minutes_since_last
    FROM events
),
session_starts AS (
    SELECT 
        *,
        CASE 
            WHEN minutes_since_last IS NULL OR minutes_since_last > 30 
            THEN 1 ELSE 0 
        END AS is_new_session
    FROM time_diff
),
sessions AS (
    SELECT 
        *,
        SUM(is_new_session) OVER (
            PARTITION BY user_id 
            ORDER BY event_time
        ) AS session_id
    FROM session_starts
)
SELECT 
    user_id,
    session_id,
    MIN(event_time) AS session_start,
    MAX(event_time) AS session_end,
    COUNT(*) AS events_in_session,
    TIMESTAMPDIFF(MINUTE, MIN(event_time), MAX(event_time)) AS duration_min
FROM sessions
GROUP BY user_id, session_id;
```

---

## 3️⃣ Funnel Analysis

### Conversion Funnel

```sql
WITH funnel AS (
    SELECT 
        user_id,
        MAX(CASE WHEN event = 'view_page' THEN 1 ELSE 0 END) AS viewed,
        MAX(CASE WHEN event = 'add_to_cart' THEN 1 ELSE 0 END) AS added_to_cart,
        MAX(CASE WHEN event = 'checkout' THEN 1 ELSE 0 END) AS checked_out,
        MAX(CASE WHEN event = 'purchase' THEN 1 ELSE 0 END) AS purchased
    FROM events
    WHERE event_date = '2024-01-15'
    GROUP BY user_id
)
SELECT 
    SUM(viewed) AS step1_view,
    SUM(added_to_cart) AS step2_cart,
    SUM(checked_out) AS step3_checkout,
    SUM(purchased) AS step4_purchase,
    ROUND(100.0 * SUM(added_to_cart) / NULLIF(SUM(viewed), 0), 1) AS view_to_cart_pct,
    ROUND(100.0 * SUM(purchased) / NULLIF(SUM(viewed), 0), 1) AS overall_conversion
FROM funnel;
```

### Funnel with Time Constraints

```sql
-- Only count as conversion if purchase within 1 hour of view
WITH events_ordered AS (
    SELECT 
        user_id,
        event,
        event_time,
        FIRST_VALUE(event_time) OVER (
            PARTITION BY user_id 
            ORDER BY event_time
        ) AS first_view_time
    FROM events
    WHERE event IN ('view_page', 'purchase')
)
SELECT 
    COUNT(DISTINCT CASE WHEN event = 'view_page' THEN user_id END) AS viewers,
    COUNT(DISTINCT CASE 
        WHEN event = 'purchase' 
        AND TIMESTAMPDIFF(HOUR, first_view_time, event_time) <= 1 
        THEN user_id 
    END) AS converted_within_1h
FROM events_ordered;
```

---

## 4️⃣ Retention Analysis

### Day 1, Day 7, Day 30 Retention

```sql
WITH first_activity AS (
    SELECT 
        user_id,
        MIN(DATE(activity_date)) AS signup_date
    FROM user_activity
    GROUP BY user_id
),
activity_days AS (
    SELECT DISTINCT
        ua.user_id,
        DATE(ua.activity_date) AS activity_date,
        fa.signup_date,
        DATEDIFF(DATE(ua.activity_date), fa.signup_date) AS days_since_signup
    FROM user_activity ua
    JOIN first_activity fa ON ua.user_id = fa.user_id
)
SELECT 
    signup_date,
    COUNT(DISTINCT user_id) AS cohort_size,
    COUNT(DISTINCT CASE WHEN days_since_signup = 1 THEN user_id END) AS day1_retained,
    COUNT(DISTINCT CASE WHEN days_since_signup = 7 THEN user_id END) AS day7_retained,
    COUNT(DISTINCT CASE WHEN days_since_signup = 30 THEN user_id END) AS day30_retained,
    ROUND(100.0 * COUNT(DISTINCT CASE WHEN days_since_signup = 1 THEN user_id END) 
          / COUNT(DISTINCT user_id), 1) AS day1_retention_pct
FROM activity_days
WHERE days_since_signup IN (0, 1, 7, 30)
GROUP BY signup_date
ORDER BY signup_date;
```

### Rolling Retention (Any Activity in First N Days)

```sql
WITH cohort AS (
    SELECT 
        user_id,
        DATE_FORMAT(MIN(activity_date), '%Y-%m') AS cohort_month
    FROM user_activity
    GROUP BY user_id
),
monthly_activity AS (
    SELECT DISTINCT
        c.user_id,
        c.cohort_month,
        DATE_FORMAT(ua.activity_date, '%Y-%m') AS activity_month
    FROM cohort c
    JOIN user_activity ua ON c.user_id = ua.user_id
)
SELECT 
    cohort_month,
    activity_month,
    PERIOD_DIFF(activity_month, cohort_month) AS months_since_signup,
    COUNT(DISTINCT user_id) AS active_users
FROM monthly_activity
GROUP BY cohort_month, activity_month
ORDER BY cohort_month, months_since_signup;
```

---

## 5️⃣ Pivoting Data

### Manual Pivot

```sql
-- Convert rows to columns
SELECT 
    product_id,
    SUM(CASE WHEN month = 'Jan' THEN revenue ELSE 0 END) AS Jan,
    SUM(CASE WHEN month = 'Feb' THEN revenue ELSE 0 END) AS Feb,
    SUM(CASE WHEN month = 'Mar' THEN revenue ELSE 0 END) AS Mar,
    SUM(CASE WHEN month = 'Apr' THEN revenue ELSE 0 END) AS Apr
FROM monthly_sales
GROUP BY product_id;

-- Dynamic pivot in MySQL (using prepared statements)
SET @sql = NULL;
SELECT GROUP_CONCAT(DISTINCT
    CONCAT('SUM(CASE WHEN month = ''', month, ''' THEN revenue ELSE 0 END) AS `', month, '`')
) INTO @sql
FROM monthly_sales;

SET @sql = CONCAT('SELECT product_id, ', @sql, ' FROM monthly_sales GROUP BY product_id');
PREPARE stmt FROM @sql;
EXECUTE stmt;
```

### Unpivot (Columns to Rows)

```sql
-- Convert columns to rows
SELECT product_id, 'Q1' AS quarter, q1_sales AS sales FROM quarterly_sales
UNION ALL
SELECT product_id, 'Q2' AS quarter, q2_sales AS sales FROM quarterly_sales
UNION ALL
SELECT product_id, 'Q3' AS quarter, q3_sales AS sales FROM quarterly_sales
UNION ALL
SELECT product_id, 'Q4' AS quarter, q4_sales AS sales FROM quarterly_sales;
```

---

## 6️⃣ Running Totals with Conditions

### Cumulative Sum with Reset

```sql
-- Reset running total when a specific event occurs
WITH events_flagged AS (
    SELECT 
        *,
        CASE WHEN event_type = 'reset' THEN 1 ELSE 0 END AS reset_flag,
        SUM(CASE WHEN event_type = 'reset' THEN 1 ELSE 0 END) OVER (
            ORDER BY event_time
        ) AS reset_group
    FROM events
)
SELECT 
    event_time,
    amount,
    SUM(CASE WHEN event_type != 'reset' THEN amount ELSE 0 END) OVER (
        PARTITION BY reset_group
        ORDER BY event_time
    ) AS running_total
FROM events_flagged;
```

### Running Total Excluding Current Row

```sql
SELECT 
    id,
    amount,
    SUM(amount) OVER (
        ORDER BY id 
        ROWS BETWEEN UNBOUNDED PRECEDING AND 1 PRECEDING
    ) AS total_before_current
FROM transactions;
```

---

## 7️⃣ Median Calculation

### Median Salary (Odd and Even Count)

```sql
WITH ranked AS (
    SELECT 
        salary,
        ROW_NUMBER() OVER (ORDER BY salary) AS rn,
        COUNT(*) OVER () AS total
    FROM employees
)
SELECT AVG(salary) AS median_salary
FROM ranked
WHERE rn IN (FLOOR((total + 1) / 2), CEIL((total + 1) / 2));
```

### Median Per Group

```sql
WITH ranked AS (
    SELECT 
        department,
        salary,
        ROW_NUMBER() OVER (PARTITION BY department ORDER BY salary) AS rn,
        COUNT(*) OVER (PARTITION BY department) AS dept_count
    FROM employees
)
SELECT 
    department,
    AVG(salary) AS median_salary
FROM ranked
WHERE rn IN (FLOOR((dept_count + 1) / 2), CEIL((dept_count + 1) / 2))
GROUP BY department;
```

---

## 8️⃣ Self-Join Patterns

### Find Pairs Meeting Condition

```sql
-- Employees who earn more than their manager
SELECT 
    e.name AS employee,
    e.salary AS employee_salary,
    m.name AS manager,
    m.salary AS manager_salary
FROM employees e
JOIN employees m ON e.manager_id = m.id
WHERE e.salary > m.salary;

-- Find duplicates
SELECT a.*
FROM orders a
JOIN orders b ON a.customer_id = b.customer_id 
    AND a.order_date = b.order_date
    AND a.id < b.id;
```

### Compare Consecutive Rows

```sql
-- Days where sales increased from previous day
SELECT 
    curr.date,
    curr.sales AS current_sales,
    prev.sales AS previous_sales,
    curr.sales - prev.sales AS increase
FROM daily_sales curr
JOIN daily_sales prev ON curr.date = DATE_ADD(prev.date, INTERVAL 1 DAY)
WHERE curr.sales > prev.sales;
```

---

## 9️⃣ Advanced Window Frame Clauses

### Different Frame Types

```sql
SELECT 
    order_date,
    amount,
    -- Running total (default)
    SUM(amount) OVER (ORDER BY order_date) AS running_total,
    
    -- Last 7 days (row-based)
    SUM(amount) OVER (
        ORDER BY order_date 
        ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
    ) AS last_7_rows,
    
    -- Last 7 days (range-based - actual dates)
    SUM(amount) OVER (
        ORDER BY order_date 
        RANGE BETWEEN INTERVAL 6 DAY PRECEDING AND CURRENT ROW
    ) AS last_7_days,
    
    -- Exclude current row
    AVG(amount) OVER (
        ORDER BY order_date
        ROWS BETWEEN UNBOUNDED PRECEDING AND 1 PRECEDING
    ) AS avg_before_current,
    
    -- Centered window
    AVG(amount) OVER (
        ORDER BY order_date
        ROWS BETWEEN 3 PRECEDING AND 3 FOLLOWING
    ) AS centered_avg_7
FROM orders;
```

### NTILE for Percentiles

```sql
-- Divide into quartiles
SELECT 
    employee_id,
    salary,
    NTILE(4) OVER (ORDER BY salary) AS quartile,
    NTILE(100) OVER (ORDER BY salary) AS percentile
FROM employees;

-- Find top 10% earners
WITH percentiled AS (
    SELECT *, NTILE(10) OVER (ORDER BY salary DESC) AS decile
    FROM employees
)
SELECT * FROM percentiled WHERE decile = 1;
```

---

## 🔟 Recursive CTE Patterns

### Hierarchy Traversal (Org Chart)

```sql
WITH RECURSIVE org_tree AS (
    -- Base: CEO (no manager)
    SELECT 
        id, 
        name, 
        manager_id, 
        name AS path,
        0 AS level
    FROM employees
    WHERE manager_id IS NULL
    
    UNION ALL
    
    -- Recursive: employees under each level
    SELECT 
        e.id, 
        e.name, 
        e.manager_id,
        CONCAT(ot.path, ' > ', e.name) AS path,
        ot.level + 1
    FROM employees e
    JOIN org_tree ot ON e.manager_id = ot.id
)
SELECT * FROM org_tree ORDER BY level, name;
```

### Bill of Materials (BOM) Explosion

```sql
WITH RECURSIVE bom AS (
    -- Base: top-level product
    SELECT 
        product_id,
        component_id,
        quantity,
        1 AS level
    FROM product_components
    WHERE product_id = 'FINAL_PRODUCT'
    
    UNION ALL
    
    -- Recursive: sub-components
    SELECT 
        pc.product_id,
        pc.component_id,
        pc.quantity * bom.quantity AS quantity,
        bom.level + 1
    FROM product_components pc
    JOIN bom ON pc.product_id = bom.component_id
)
SELECT 
    component_id,
    SUM(quantity) AS total_needed
FROM bom
GROUP BY component_id;
```

### Generate Date Series

```sql
-- Generate all dates in a range
WITH RECURSIVE dates AS (
    SELECT DATE('2024-01-01') AS date
    UNION ALL
    SELECT DATE_ADD(date, INTERVAL 1 DAY)
    FROM dates
    WHERE date < '2024-12-31'
)
SELECT * FROM dates;

-- Fill missing dates with zeros
WITH RECURSIVE dates AS (
    SELECT MIN(order_date) AS date FROM orders
    UNION ALL
    SELECT DATE_ADD(date, INTERVAL 1 DAY)
    FROM dates
    WHERE date < (SELECT MAX(order_date) FROM orders)
)
SELECT 
    d.date,
    COALESCE(SUM(o.amount), 0) AS daily_revenue
FROM dates d
LEFT JOIN orders o ON d.date = o.order_date
GROUP BY d.date;
```

---

## 1️⃣1️⃣ NULL Handling Tricks

```sql
-- COALESCE returns first non-NULL
SELECT COALESCE(column1, column2, 'default') FROM table;

-- NULLIF returns NULL if equal (avoid division by zero)
SELECT sales / NULLIF(visits, 0) AS conversion_rate FROM stats;

-- Count NULLs
SELECT 
    COUNT(*) AS total_rows,
    COUNT(column) AS non_null_count,
    COUNT(*) - COUNT(column) AS null_count
FROM table;

-- IFNULL / NVL equivalents
SELECT IFNULL(column, 0) FROM table;  -- MySQL
SELECT NVL(column, 0) FROM table;     -- Oracle
SELECT ISNULL(column, 0) FROM table;  -- SQL Server
```

---

## 1️⃣2️⃣ String Manipulation

```sql
-- Parse JSON (MySQL 8+)
SELECT JSON_EXTRACT(data, '$.user.name') AS user_name FROM logs;

-- Split string and get Nth element
SELECT SUBSTRING_INDEX(SUBSTRING_INDEX(full_name, ' ', 2), ' ', -1) AS middle_name
FROM users;

-- Regex matching
SELECT * FROM logs WHERE message REGEXP '^Error.*timeout$';

-- String aggregation
SELECT 
    department,
    GROUP_CONCAT(name ORDER BY name SEPARATOR ', ') AS employees
FROM employees
GROUP BY department;
```

---

## 📝 Tricky Interview Questions

### Q1: Delete Duplicate Rows (Keep One)

```sql
-- Keep lowest ID for each duplicate
DELETE e1 FROM emails e1
JOIN emails e2 ON e1.email = e2.email AND e1.id > e2.id;

-- Alternative with CTE
WITH duplicates AS (
    SELECT id, ROW_NUMBER() OVER (PARTITION BY email ORDER BY id) AS rn
    FROM emails
)
DELETE FROM emails WHERE id IN (SELECT id FROM duplicates WHERE rn > 1);
```

### Q2: Find Missing Numbers

```sql
-- Find gaps in sequence 1 to MAX
WITH RECURSIVE numbers AS (
    SELECT 1 AS n
    UNION ALL
    SELECT n + 1 FROM numbers WHERE n < (SELECT MAX(id) FROM products)
)
SELECT n AS missing_id
FROM numbers
WHERE n NOT IN (SELECT id FROM products);
```

### Q3: Rank Without Window Function

```sql
-- Dense rank without DENSE_RANK()
SELECT 
    score,
    (SELECT COUNT(DISTINCT score) 
     FROM scores s2 
     WHERE s2.score >= s1.score) AS rank
FROM scores s1
ORDER BY score DESC;
```

### Q4: Swap Values Between Rows

```sql
-- Swap consecutive IDs (1↔2, 3↔4, etc.)
SELECT 
    CASE 
        WHEN id % 2 = 1 AND id + 1 <= (SELECT MAX(id) FROM students) THEN id + 1
        WHEN id % 2 = 0 THEN id - 1
        ELSE id
    END AS id,
    name
FROM students
ORDER BY id;
```

### Q5: First Non-Repeated Character

```sql
-- Find first character that appears only once in each string
WITH chars AS (
    SELECT 
        id,
        SUBSTRING(str, n, 1) AS char,
        n AS position
    FROM strings
    CROSS JOIN (
        SELECT 1 AS n UNION SELECT 2 UNION SELECT 3 UNION SELECT 4 UNION SELECT 5
        -- Extend as needed
    ) nums
    WHERE n <= LENGTH(str)
)
SELECT id, MIN(char) KEEP (DENSE_RANK FIRST ORDER BY position) AS first_unique
FROM chars c
WHERE (SELECT COUNT(*) FROM chars c2 WHERE c2.id = c.id AND c2.char = c.char) = 1
GROUP BY id;
```

---

## 🔑 Key Patterns Summary

| Pattern | When to Use |
|---------|-------------|
| Gaps & Islands | Consecutive sequences, streaks |
| Sessionization | Group events by time gaps |
| Funnel Analysis | Conversion tracking |
| Retention | Cohort analysis |
| Pivoting | Rows ↔ Columns transformation |
| Recursive CTE | Hierarchies, series generation |
| Window Frames | Rolling calculations |

---

*Last Updated: 2024*
