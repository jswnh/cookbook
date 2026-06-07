# MS SQL Server Data Manipulation Language (DML) Syntax

This reference covers querying, data modifications, common table expressions, window functions, and bulk merges in SQL Server using Transact-SQL (T-SQL).

---

## 1. Querying Data (SELECT)

### SELECT TOP vs. OFFSET FETCH (Paging)
SQL Server uses `TOP` for quick limits, and `OFFSET FETCH` for pagination.

```sql
-- 1. Get top 10 records
SELECT TOP 10 product_id, price 
FROM production.products 
ORDER BY price DESC;

-- 2. Get top 10 percent of records
SELECT TOP 10 PERCENT product_id, price 
FROM production.products 
ORDER BY price DESC;

-- 3. OFFSET FETCH Pagination (SQL Server 2012+)
-- Skips first 20 rows, returns next 10. Requires ORDER BY.
SELECT product_id, price 
FROM production.products 
ORDER BY product_id
OFFSET 20 ROWS
FETCH NEXT 10 ROWS ONLY;
```

### SELECT INTO (Dynamic Table Creation)
Creates a new table dynamically in the database schema and copies the schema and data from an existing query result.

```sql
SELECT product_id, price 
INTO production.products_backup -- Creates products_backup table on the fly
FROM production.products 
WHERE price > 100.00;
```

---

## 2. Multi-Table Updates & Deletes

SQL Server supports joining tables inside `UPDATE` and `DELETE` queries.

```sql
-- 1. Multi-Table Update
UPDATE e
SET e.salary = e.salary * 1.10
FROM hr.employees e
INNER JOIN hr.departments d ON e.department_id = d.id
WHERE d.location = 'Seattle';

-- 2. Multi-Table Delete
DELETE e
FROM hr.employees e
INNER JOIN hr.departments d ON e.department_id = d.id
WHERE d.status = 'Inactive';
```

---

## 3. The OUTPUT Clause

The `OUTPUT` clause intercepts modifications and returns data from the changed rows to the client or writes them to temporary tables. It uses two virtual tables: `inserted` (new values) and `deleted` (previous values).

```sql
-- 1. Output inserted identity keys and default dates during INSERT
INSERT INTO sales.customers (name)
OUTPUT inserted.id, inserted.created_at
VALUES ('John Doe');

-- 2. Capture audit logs during UPDATE into a Table Variable
DECLARE @UpdateAudit TABLE (id INT, old_name NVARCHAR(100), new_name NVARCHAR(100));

UPDATE sales.customers
SET name = 'Jane Doe'
OUTPUT inserted.id, deleted.name, inserted.name 
    INTO @UpdateAudit
WHERE id = 42;

-- 3. Capture metadata during DELETE
DELETE FROM sales.customers
OUTPUT deleted.id, deleted.name
WHERE id = 5;
```

---

## 4. Common Table Expressions (CTEs)

### Standard CTE
```sql
WITH Sales_CTE (sales_rep_id, total_sales) AS (
    SELECT sales_rep_id, SUM(amount)
    FROM sales.orders
    GROUP BY sales_rep_id
)
SELECT sales_rep_id, total_sales
FROM Sales_CTE
WHERE total_sales > 10000.00;
```

### Recursive CTE (Organizational Trees)
```sql
WITH Org_CTE AS (
    -- Anchor Member
    SELECT employee_id, manager_id, name, 1 AS depth
    FROM hr.employees
    WHERE manager_id IS NULL
    
    UNION ALL
    
    -- Recursive Member
    SELECT e.employee_id, e.manager_id, e.name, o.depth + 1
    FROM hr.employees e
    INNER JOIN Org_CTE o ON e.manager_id = o.employee_id
)
SELECT employee_id, manager_id, name, depth 
FROM Org_CTE;
```

---

## 5. Window Functions

SQL Server supports standard analytical rankings and aggregations over query partitions.

```sql
SELECT 
    name, 
    department_id, 
    salary,
    -- Unique rank index
    ROW_NUMBER() OVER (PARTITION BY department_id ORDER BY salary DESC) AS row_num,
    -- Cumulative running total of salaries inside department
    SUM(salary) OVER (
        PARTITION BY department_id 
        ORDER BY hire_date
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS running_total
FROM hr.employees;
```

---

## 6. Standard MERGE Statement

Inserts, updates, or deletes target records based on source changes inside a single execution pass.

```sql
MERGE sales.inventory AS target
USING sales.new_stock AS source
ON target.product_id = source.product_id
WHEN MATCHED AND (target.stock_count + source.quantity < 0) THEN
    DELETE
WHEN MATCHED THEN
    UPDATE SET target.stock_count = target.stock_count + source.quantity
WHEN NOT MATCHED THEN
    INSERT (product_id, stock_count) VALUES (source.product_id, source.quantity);
```
- **`target` / `source`**: Define target and source tables.
- **`WHEN MATCHED`**: Modifies matched rows.
- **`WHEN NOT MATCHED`**: Inserts missing rows.
