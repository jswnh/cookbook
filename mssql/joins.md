## 1. Standard Joins

### Inner Join

```sql
SELECT e.employee_id, e.name, d.name AS dept_name
FROM hr.employees e
INNER JOIN hr.departments d ON e.department_id = d.id;
```

### Left and Right Outer Joins

- **`LEFT JOIN`** (or `LEFT OUTER JOIN`): Returns all rows from the left table.
- **`RIGHT JOIN`** (or `RIGHT OUTER JOIN`): Returns all rows from the right table.

```sql
SELECT d.name AS dept_name, e.name
FROM hr.departments d
LEFT JOIN hr.employees e ON d.id = e.department_id;
```

### Full Outer Join (Natively Supported)

Returns all records when there is a match in either left or right table. Missing values on either side are filled with `NULL`.

```sql
SELECT e.name, d.name AS dept_name
FROM hr.employees e
FULL OUTER JOIN hr.departments d ON e.department_id = d.id;
```

---

## 2. Cross Join & Self Join

### Cross Join

Returns the Cartesian product of the two tables.

```sql
SELECT p.product_name, s.store_name
FROM sales.products p
CROSS JOIN sales.stores s;
```

### Self Join

Joins a table to itself, typically using table aliases to represent hierarchy.

```sql
SELECT
    e.name AS employee_name,
    m.name AS manager_name
FROM hr.employees e
LEFT JOIN hr.employees m ON e.manager_id = m.id;
```

---

## 3. APPLY Operators (CROSS APPLY / OUTER APPLY)

The `APPLY` operators allow you to join a left-side table to a right-side table-valued function or correlated subquery. The right side is evaluated for every row of the left side.

### CROSS APPLY

Equivalent to an `INNER JOIN`. Returns rows from the left table only if the right-side expression returns at least one row.

```sql
-- For each department, find the top 2 highest paid employees
SELECT d.name AS dept_name, top_emp.name, top_emp.salary
FROM hr.departments d
CROSS APPLY (
    SELECT TOP 2 name, salary
    FROM hr.employees e
    WHERE e.department_id = d.id
    ORDER BY salary DESC
) AS top_emp;
```

### OUTER APPLY

Equivalent to a `LEFT OUTER JOIN`. Returns rows from the left table even if the right-side expression returns no rows (with `NULL` values for the right-side columns).

```sql
-- For each department, find the top 2 highest paid employees, retaining empty departments
SELECT d.name AS dept_name, top_emp.name, top_emp.salary
FROM hr.departments d
OUTER APPLY (
    SELECT TOP 2 name, salary
    FROM hr.employees e
    WHERE e.department_id = d.id
    ORDER BY salary DESC
) AS top_emp;
```

---

## 4. Join Algorithm Hints

SQL Server's query optimizer automatically chooses the best join algorithm (Nested Loops, Hash Match, or Merge Join). However, you can explicitly force a join algorithm using T-SQL hints.

```sql
-- Force a Hash Join
SELECT *
FROM hr.employees e
INNER HASH JOIN hr.departments d ON e.department_id = d.id;

-- Force a Loop Join (Nested Loop)
SELECT *
FROM hr.employees e
INNER LOOP JOIN hr.departments d ON e.department_id = d.id;

-- Force a Merge Join
SELECT *
FROM hr.employees e
INNER MERGE JOIN hr.departments d ON e.department_id = d.id;
```

> [!WARNING]
> Forcing join algorithms is generally discouraged in production code. As data sizes grow or indexes change, a hardcoded join type can lead to severe performance degradation. Let the optimizer choose the algorithm.
