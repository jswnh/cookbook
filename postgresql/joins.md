## 1. Standard Joins

### Inner Join
Returns records that have matching values in both tables.

```sql
SELECT 
    e.id, 
    e.name AS employee_name, 
    d.name AS department_name
FROM employees e
INNER JOIN departments d ON e.department_id = d.id;
```

### Left and Right Outer Joins
- **`LEFT JOIN`**: Returns all rows from the left table, plus matching rows from the right table. Unmatched right rows return `NULL`.
- **`RIGHT JOIN`**: Returns all rows from the right table, plus matching rows from the left table. Unmatched left rows return `NULL`.

```sql
-- LEFT OUTER JOIN
SELECT d.name AS department_name, e.name
FROM departments d
LEFT JOIN employees e ON d.id = e.department_id;
```

### Full Outer Join (Natively Supported)
Returns all rows when there is a match in either the left or right table. Missing values on either side are filled with `NULL`.

```sql
SELECT e.name, d.name AS department_name
FROM employees e
FULL OUTER JOIN departments d ON e.department_id = d.id;
```

---

## 2. Cross Join & Self Join

### Cross Join (Cartesian Product)
Pairs every row of the first table with every row of the second.

```sql
SELECT p.name AS product_name, c.color_name
FROM products p
CROSS JOIN colors c;
```

### Self Join
Joins a table with itself, typically used for querying recursive relationships within a single dataset.

```sql
SELECT 
    e.name AS employee_name, 
    m.name AS manager_name
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.id;
```

---

## 3. Lateral Joins (LATERAL Subqueries)

A `LATERAL` join acts like a SQL `foreach` loop. It allows a subquery in the `FROM` clause to reference columns from preceding tables in the `FROM` list.

```sql
-- For each department, fetch the top 2 highest-paid employees
SELECT d.name AS department_name, top_emp.name, top_emp.salary
FROM departments d
LEFT JOIN LATERAL (
    SELECT name, salary
    FROM employees e
    WHERE e.department_id = d.id
    ORDER BY salary DESC
    LIMIT 2
) top_emp ON TRUE;
```
> [!NOTE]
> Without the `LATERAL` keyword, the inner subquery cannot access `d.id` and will fail with a compile error.

---

## 4. Join Algorithms in the Query Planner

When executing queries, PostgreSQL's optimizer chooses from three core join algorithms:

### 1. Nested Loop Join
For each row in the outer table, scans the inner table for matching keys.
- **Ideal for**: Small datasets or when the inner table has a highly selective index on the join key.

### 2. Hash Join
Builds an in-memory hash table on the join keys of the smaller table, then scans the larger table, hashing its keys to find matches in the hash table.
- **Ideal for**: Large, unsorted datasets where join keys lack usable indexes.

### 3. Merge Join
Sorts both datasets on the join key (if not already sorted by an index), then scans both in parallel to merge matches.
- **Ideal for**: Large tables that are already sorted or indexed on the join key.

```sql
-- View which join algorithm is selected
EXPLAIN SELECT * FROM orders o JOIN customers c ON o.customer_id = c.id;
```
