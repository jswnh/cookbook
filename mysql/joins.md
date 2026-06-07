# MySQL Joins Syntax & Reference

Joins combine rows from two or more tables based on a related column. MySQL supports inner joins, outer joins (left/right), cross joins, self joins, and emulations for full outer joins.

---

## 1. Inner Join

Returns records that have matching values in both tables.

```sql
SELECT 
    e.id, 
    e.first_name, 
    e.last_name, 
    d.name AS department_name
FROM employees e
INNER JOIN departments d ON e.department_id = d.id;
```

---

## 2. Left and Right Outer Joins

### LEFT JOIN (or LEFT OUTER JOIN)
Returns all records from the left table and matched records from the right table. Unmatched right-side rows return `NULL`.

```sql
SELECT 
    d.name AS department_name, 
    e.first_name, 
    e.last_name
FROM departments d
LEFT JOIN employees e ON d.id = e.department_id;
```

### RIGHT JOIN (or RIGHT OUTER JOIN)
Returns all records from the right table and matched records from the left table. Unmatched left-side rows return `NULL`.

```sql
SELECT 
    e.first_name, 
    e.last_name, 
    d.name AS department_name
FROM employees e
RIGHT JOIN departments d ON e.department_id = d.id;
```

---

## 3. Emulating FULL OUTER JOIN

MySQL **does not** natively support `FULL OUTER JOIN`. To get all rows from both tables, matching where possible and returning `NULL` for missing relations, combine a `LEFT JOIN` and a `RIGHT JOIN` using `UNION`.

```sql
SELECT e.id, e.first_name, d.name AS department_name
FROM employees e
LEFT JOIN departments d ON e.department_id = d.id

UNION

SELECT e.id, e.first_name, d.name AS department_name
FROM employees e
RIGHT JOIN departments d ON e.department_id = d.id;
```
> [!TIP]
> Using `UNION` automatically eliminates duplicate rows resulting from the overlapping inner matches. If you want to retain duplicates, use `UNION ALL`.

---

## 4. Cross Join (Cartesian Product)

Returns the Cartesian product of the two tables (every row from the first table combined with every row from the second).

```sql
-- Explicit Syntax
SELECT p.product_name, s.store_name
FROM products p
CROSS JOIN stores s;

-- Implicit Syntax
SELECT p.product_name, s.store_name
FROM products p, stores s;
```

---

## 5. Self Join

A self join is a regular join in which a table is joined with itself. It is useful for querying hierarchical data stored in a single table.

```sql
SELECT 
    e.name AS employee_name, 
    m.name AS manager_name
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.id;
```

---

## 6. Join Performance & Execution Plans

MySQL 8.0+ has an improved query optimizer that uses different strategies to execute joins.

### Hash Joins vs. Block Nested Loop (BNL)
- **Nested Loop Join**: Reads rows from the outer table one-by-one and checks for matches in the inner table. Optimal when join keys are indexed.
- **Hash Join (MySQL 8.0.18+)**: Used when join keys are not indexed. It builds an in-memory hash table on the smaller join source and scans the larger source against it. Replaces the legacy Block Nested Loop algorithm.

```sql
-- Examine the execution plan to see if a Hash Join is used
EXPLAIN FORMAT=TREE 
SELECT * FROM orders o 
JOIN customers c ON o.contact_email = c.email;
```

### Straight Join (`STRAIGHT_JOIN`)
Forces MySQL to join tables in the exact order they are listed in the `FROM` clause, bypassing the optimizer's join-ordering algorithms.

```sql
SELECT * 
FROM orders o 
STRAIGHT_JOIN customers c ON o.customer_id = c.id;
```
> [!WARNING]
> Use `STRAIGHT_JOIN` with caution. Only apply it when you are certain that the optimizer is choosing a sub-optimal join order, as data distributions change over time.
