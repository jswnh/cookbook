## 1. Querying Data (SELECT)

### Standard SELECT Structure with Custom Null Sorting

```sql
SELECT
    department_id,
    COUNT(*) AS employee_count,
    AVG(salary) AS avg_salary
FROM employees
WHERE hire_date > '2020-01-01'
GROUP BY department_id
HAVING AVG(salary) > 60000.00
ORDER BY avg_salary DESC NULLS LAST, employee_count ASC
LIMIT 10 OFFSET 20;
```

- **`NULLS LAST` / `NULLS FIRST`**: Controls where null values appear in the sorted order, regardless of `ASC` or `DESC` setting.

---

## 2. Data Modification (INSERT, UPDATE, DELETE)

### RETURNING Clause (Insert / Update / Delete)

Retrieves values from rows modified by the command instantly. Avoids making follow-up queries to check defaults or auto-generated keys.

```sql
-- 1. Insert and retrieve the auto-generated identity ID and default timestamp
INSERT INTO users (username)
VALUES ('johndoe')
RETURNING id, created_at;

-- 2. Update and retrieve the old or new values
UPDATE employees
SET salary = salary * 1.10
WHERE id = 42
RETURNING name, salary AS new_salary;

-- 3. Delete and retrieve the deleted metadata
DELETE FROM active_sessions
WHERE expires_at < NOW()
RETURNING session_id;
```

### PostgreSQL Upsert (ON CONFLICT)

Resolves constraint conflicts natively during insertion. Requires a target unique key or index name.

```sql
-- Option A: Do nothing on conflict
INSERT INTO tags (name)
VALUES ('SQL')
ON CONFLICT (name) DO NOTHING;

-- Option B: Update properties on conflict (using the virtual EXCLUDED table)
INSERT INTO visitor_stats (ip_address, visit_count)
VALUES ('192.168.1.1', 1)
ON CONFLICT (ip_address)
DO UPDATE SET
    visit_count = visitor_stats.visit_count + EXCLUDED.visit_count,
    last_visited_at = CURRENT_TIMESTAMP;
```

- **`EXCLUDED`**: A virtual table representing the row initially proposed for insertion.

### Multi-Table Operations (USING / FROM)

PostgreSQL handles multi-table updates and deletes using the `USING` and `FROM` clauses instead of inline joins.

```sql
-- 1. Update using FROM
UPDATE employees e
SET salary = e.salary * 1.10
FROM departments d
WHERE e.department_id = d.id AND d.location = 'San Francisco';

-- 2. Delete using USING
DELETE FROM employees e
USING departments d
WHERE e.department_id = d.id AND d.location = 'Offshore';
```

---

## 3. Common Table Expressions (CTEs)

PostgreSQL supports non-recursive and recursive CTEs, and allows controlling whether the CTE result is cached (materialized) or inlined.

### CTE Syntax and Materialization Control

```sql
-- FORCE PG NOT TO MATERIALIZE (inlines subquery, optimizing index usage)
WITH active_users AS NOT MATERIALIZED (
    SELECT id, username FROM users WHERE is_active = TRUE
)
SELECT u.username, p.bio
FROM active_users u
INNER JOIN profiles p ON u.id = p.user_id;
```

### Recursive CTE Example

```sql
WITH RECURSIVE category_tree AS (
    -- Anchor Member
    SELECT id, name, parent_id, name::text AS path
    FROM categories
    WHERE parent_id IS NULL

    UNION ALL

    -- Recursive Member
    SELECT c.id, c.name, c.parent_id, (t.path || ' > ' || c.name) AS path
    FROM categories c
    INNER JOIN category_tree t ON c.parent_id = t.id
)
SELECT id, name, path FROM category_tree;
```

---

## 4. Window Functions

PostgreSQL features standard window rankings and aggregations, supporting complex partitions and frames.

```sql
SELECT
    name,
    department_id,
    salary,
    -- Simple row positioning
    ROW_NUMBER() OVER (PARTITION BY department_id ORDER BY salary DESC) AS row_num,
    -- Rolling aggregate within department over time
    SUM(salary) OVER (
        PARTITION BY department_id
        ORDER BY hire_date
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS rolling_dept_cost
FROM employees;
```

---

## 5. Standard MERGE Statement (PostgreSQL 15+)

Performs INSERT, UPDATE, or DELETE operations on a target table based on the results of a join with a source table.

```sql
MERGE INTO inventory t
USING new_stock s
ON t.product_id = s.product_id
WHEN MATCHED THEN
    UPDATE SET stock_count = t.stock_count + s.quantity
WHEN NOT MATCHED THEN
    INSERT (product_id, stock_count) VALUES (s.product_id, s.quantity);
```

- **`WHEN MATCHED`**: Specifies behavior if the source row matches a target row.
- **`WHEN NOT MATCHED`**: Specifies behavior if the source row doesn't match any target rows.
