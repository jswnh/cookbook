# MySQL Indexes & Query Optimization

Indexes are data structures (typically B-Trees in InnoDB) that allow MySQL to locate rows rapidly without scanning the entire table.

---

## 1. Index Types

### Clustered Index (Primary Key)
InnoDB tables store rows physically sorted by the primary key. If no primary key is defined, InnoDB chooses the first unique index without NULLs or generates a hidden row ID.

### Secondary Indexes
Non-clustered indexes. They store the indexed column values and a pointer (the Primary Key value) back to the clustered index row.

### Index Creation Syntax

```sql
-- 1. Create a standard secondary B-Tree Index
CREATE INDEX idx_users_lastname ON users (last_name);

-- 2. Create a Unique Index (rejects duplicate values)
CREATE UNIQUE INDEX uq_users_username ON users (username);

-- 3. Create a Fulltext Index (for search in TEXT columns)
CREATE FULLTEXT INDEX ft_articles_body ON articles (body);

-- 4. Create a Spatial Index (requires NOT NULL spatial column)
CREATE SPATIAL INDEX sp_geom ON locations (coordinate);

-- 5. Drop an Index
DROP INDEX idx_users_lastname ON users;
```

---

## 2. Advanced Indexing Strategies

### Composite Indexes (Leftmost Prefix Rule)
An index on multiple columns, e.g., `(col_a, col_b, col_c)`.
MySQL can use this index for queries filtering:
- `col_a`
- `col_a` AND `col_b`
- `col_a` AND `col_b` AND `col_c`

> [!WARNING]
> If a query filters on `col_b` and `col_c` without `col_a`, the index **cannot** be used because the leftmost prefix is missing.

```sql
CREATE INDEX idx_sales_date_dept ON sales (sales_date, department_id);
```

### Prefix Indexes (VARCHAR Optimization)
For large text columns, index only the first $N$ characters to save space and speed up writes.
```sql
-- Index only the first 10 characters of email
CREATE INDEX idx_users_email_prefix ON users (email(10));
```

### Functional Indexes (MySQL 8.0.13+)
Allows indexing values returned by functions or scalar expressions instead of direct column values.
```sql
-- Index the year part of a date to optimize YEAR(signup_date) queries
CREATE INDEX idx_signup_year ON users ((YEAR(signup_date)));

-- Index lowercase values to speed up case-insensitive searches
CREATE INDEX idx_lower_email ON users ((LOWER(email)));
```
> [!IMPORTANT]
> The expression inside a functional index must be enclosed in double parentheses.

### Invisible Indexes (MySQL 8.0+)
Indexes can be toggled invisible. The optimizer ignores invisible indexes but they are still updated on DML operations. This helps test if dropping an index affects performance before committing to a drop.

```sql
-- Make an index invisible
ALTER TABLE users ALTER INDEX idx_users_lastname INVISIBLE;

-- Make it visible again
ALTER TABLE users ALTER INDEX idx_users_lastname VISIBLE;
```

---

## 3. Query Analysis with EXPLAIN

Prepend `EXPLAIN` to a query to see the optimizer's execution plan.

```sql
EXPLAIN SELECT * FROM users WHERE email = 'john@example.com';
```

### Key EXPLAIN Columns to Monitor

- **`type`**: The join type. Indicates how rows are retrieved. Order from best to worst:
  1. `system`: The table has only one row (const system table).
  2. `const`: Table has at most one matching row, read at query start (optimized fast lookup, e.g. primary key lookups).
  3. `eq_ref`: One row is read from this table for each combination of rows from the previous table (primary key or unique join).
  4. `ref`: All matching rows are read (secondary index lookup).
  5. `range`: Retrieves rows in a given range, using an index (e.g. `col > 10`, `BETWEEN`, `IN`).
  6. `index`: Full index scan (scans the index tree instead of table).
  7. `ALL`: Full table scan. **Should be avoided on large tables.**
- **`possible_keys`**: Indexes MySQL could choose to use.
- **`key`**: The actual index chosen by the optimizer.
- **`key_len`**: The length of the key MySQL decided to use (in bytes). Helps verify composite index utilization.
- **`rows`**: Estimate of rows MySQL must examine to execute the query.
- **`filtered`**: Estimate of the percentage of rows filtered by table conditions (scale of 0.00 to 100.00).
- **`Extra`**: Additional details. Watch out for:
  - `Using index`: Covering index used (no table lookup lookup needed). Excellent!
  - `Using filesort`: MySQL must do an extra pass to find out how to retrieve rows in sorted order. Bad!
  - `Using temporary`: MySQL needs to create a temporary table to hold results (e.g. in some GROUP BY/DISTINCT statements). Bad!

---

## 4. Optimizer Index Hints

If the query optimizer selects an inefficient plan, you can suggest or force specific indexes.

### USE INDEX
Suggests MySQL to use a specific index.
```sql
SELECT * FROM users USE INDEX (idx_users_lastname) WHERE last_name = 'Smith';
```

### FORCE INDEX
Forces MySQL to use the index, overriding the optimizer's internal cost estimation (unless no matching index lookup is possible).
```sql
SELECT * FROM users FORCE INDEX (idx_users_lastname) WHERE last_name = 'Smith';
```

### IGNORE INDEX
Tells MySQL not to use specific indexes.
```sql
SELECT * FROM users IGNORE INDEX (idx_users_lastname) WHERE last_name = 'Smith';
```
