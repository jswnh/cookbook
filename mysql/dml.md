# MySQL Data Manipulation Language (DML) Syntax

DML commands manipulate the data stored within table structures. This reference covers querying, insertion, updates, deletes, CTEs, and Window Functions using the latest MySQL standards.

---

## 1. Querying Data (SELECT)

### Standard SELECT Structure
```sql
SELECT 
    department_id, 
    COUNT(*) AS employee_count, 
    AVG(salary) AS avg_salary
FROM employees
WHERE hire_date > '2020-01-01'
GROUP BY department_id
HAVING avg_salary > 60000.00
ORDER BY avg_salary DESC, employee_count ASC
LIMIT 10 OFFSET 20; -- Pagination: skip 20 rows, fetch next 10
```

### Logical Query Processing Order
When writing queries, remember the engine processes clauses in this order:
1. `FROM` (joins, subqueries)
2. `WHERE` (filters rows)
3. `GROUP BY` (groups rows)
4. `HAVING` (filters grouped rows)
5. `SELECT` (evaluates output expressions and aliases)
6. `DISTINCT` (deduplicates)
7. `ORDER BY` (sorts rows)
8. `LIMIT` / `OFFSET` (paginates)

---

## 2. Data Modification (INSERT, UPDATE, DELETE)

### INSERT Options

#### 1. Standard Multi-row Insert
```sql
INSERT INTO customers (first_name, last_name, email)
VALUES 
    ('John', 'Doe', 'john.doe@example.com'),
    ('Jane', 'Smith', 'jane.smith@example.com');
```

#### 2. INSERT IGNORE
Prevents errors on duplicate keys; duplicate rows are discarded as warnings.
```sql
INSERT IGNORE INTO tags (tag_name) VALUES ('MySQL'), ('Database');
```

#### 3. INSERT INTO SELECT
```sql
INSERT INTO premium_customers (customer_id, points)
SELECT id, total_spent * 0.1
FROM users
WHERE status = 'active';
```

#### 4. Upsert (ON DUPLICATE KEY UPDATE)
If a duplicate primary key or unique index conflict occurs, MySQL updates the existing row instead.
```sql
INSERT INTO visitor_stats (ip_address, visit_count)
VALUES ('192.168.1.1', 1)
ON DUPLICATE KEY UPDATE 
    visit_count = visit_count + 1,
    last_visited_at = CURRENT_TIMESTAMP;
```

### UPDATE Options

#### 1. Single-Table Update
```sql
UPDATE employees 
SET salary = salary * 1.05, performance_rating = 'Excellent'
WHERE id = 42;
```

#### 2. Multi-Table Update (MySQL Specific)
Updates columns in one table based on matching values in another table.
```sql
UPDATE employees e
INNER JOIN departments d ON e.department_id = d.id
SET e.salary = e.salary * 1.10
WHERE d.location = 'San Francisco';
```

### DELETE Options

#### 1. Standard Single-Table Delete
```sql
DELETE FROM sessions 
WHERE last_activity < NOW() - INTERVAL 30 DAY;
```

#### 2. Multi-Table Delete (MySQL Specific)
Deletes rows from target tables by joining them with source tables.
```sql
-- Delete employees who belong to departments located in 'Offshore'
DELETE e
FROM employees e
INNER JOIN departments d ON e.department_id = d.id
WHERE d.location = 'Offshore';
```

---

## 3. Common Table Expressions (CTEs)

CTEs provide a highly readable way to structure complex subqueries.

### Standard Non-Recursive CTE
```sql
WITH regional_sales AS (
    SELECT region, SUM(amount) AS total_sales
    FROM orders
    GROUP BY region
),
top_regions AS (
    SELECT region
    FROM regional_sales
    WHERE total_sales > (SELECT SUM(total_sales)/10 FROM regional_sales)
)
SELECT region, total_sales
FROM regional_sales
WHERE region IN (SELECT region FROM top_regions);
```

### Recursive CTE
Mainly used for hierarchical data (e.g., organizational charts, category trees).
```sql
WITH RECURSIVE org_chart AS (
    -- Anchor member
    SELECT id, name, manager_id, 1 AS depth
    FROM employees
    WHERE manager_id IS NULL
    
    UNION ALL
    
    -- Recursive member
    SELECT e.id, e.name, e.manager_id, o.depth + 1
    FROM employees e
    INNER JOIN org_chart o ON e.manager_id = o.id
)
SELECT id, name, manager_id, depth 
FROM org_chart 
ORDER BY depth, id;
```

---

## 4. Window Functions

Window functions compute values over a defined set of rows (the "window"), without grouping the final result rows. Available in MySQL 8.0+.

### Ranking Window Functions
```sql
SELECT 
    name, 
    department_id, 
    salary,
    ROW_NUMBER() OVER (PARTITION BY department_id ORDER BY salary DESC) AS row_num,
    RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) AS rnk,
    DENSE_RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) AS dense_rnk
FROM employees;
```
- **`ROW_NUMBER()`**: Unique sequence index. (e.g. 1, 2, 3, 4)
- **`RANK()`**: Assigns same rank to duplicates, skips numbers next in line. (e.g. 1, 2, 2, 4)
- **`DENSE_RANK()`**: Assigns same rank to duplicates, does not skip numbers. (e.g. 1, 2, 2, 3)

### Value Window Functions
Access values from preceding or succeeding rows relative to the current row.
```sql
SELECT 
    order_date, 
    amount,
    -- Get sale amount from the previous order
    LAG(amount, 1, 0.00) OVER (ORDER BY order_date) AS prev_order_amount,
    -- Get sale amount from the next order
    LEAD(amount, 1, 0.00) OVER (ORDER BY order_date) AS next_order_amount
FROM orders;
```

### Aggregate Window Functions
Compute rolling totals, moving averages, etc.
```sql
SELECT 
    order_date,
    amount,
    -- Running total of sales from beginning to current row
    SUM(amount) OVER (
        ORDER BY order_date 
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS running_total
FROM orders;
```
- **`ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING`**: Bounds the window to the current row, the immediate predecessor, and the immediate successor.
- **`UNBOUNDED PRECEDING`**: The window starts at the first row of the partition.
