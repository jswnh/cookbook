# PostgreSQL Indexes & Query Optimization Reference

PostgreSQL features a highly extensible index interface, offering diverse index types tailored to JSONB, range data, spatial coordinate geometry, and high-volume chronological datasets.

---

## 1. Index Types

PostgreSQL provides several physical index strategies:

| Index Type | Keyword | Ideal Use Case | Notes |
| :--- | :--- | :--- | :--- |
| **`B-Tree`** | `btree` | Default index. Equality, ranges, sorting, prefix matches. | Highly optimized. |
| **`Hash`** | `hash` | Simple equality comparisons (`=`). | Transaction-safe since PG 10. |
| **`GIN`** | `gin` | Generalized Inverted Index. Composite items (JSONB keys/values, Arrays, Full-text). | Slow write speed, fast search. |
| **`GiST`** | `gist` | Generalized Search Tree. Overlapping values, geometric shapes, range coordinates. | Highly customisable. |
| **`BRIN`** | `brin` | Block Range Index. Ultra-large, physical-order sorted tables (e.g. timeseries timestamps). | Insignificant storage footprint. |

### Index Creation Syntax

```sql
-- 1. Create default B-Tree index
CREATE INDEX idx_users_lastname ON users (last_name);

-- 2. Create GIN index (highly optimized for JSONB columns)
CREATE INDEX idx_logs_payload ON client_logs USING gin (payload);

-- 3. Create BRIN index (analyzes blocks of rows, e.g. every 128 pages)
CREATE INDEX idx_metrics_timestamp ON system_metrics USING brin (logged_at);

-- 4. Drop index
DROP INDEX idx_users_lastname;
```

---

## 2. Advanced Indexing Strategies

### Partial Indexes (Indexed Subsets)
Indexes only rows matching a conditional clause, reducing index size and write overhead.
```sql
-- Index only active accounts
CREATE INDEX idx_active_users_email 
    ON users (email) 
    WHERE is_active = TRUE;
```

### Expression / Functional Indexes
Indexes values returned by expressions, making queries using those expressions fast.
```sql
-- Index values converted to lowercase
CREATE INDEX idx_lower_username ON users (lower(username));

-- Index date parts
CREATE INDEX idx_order_year ON orders (extract(year from order_date));
```

### Covering Indexes (INCLUDE Clause)
Appends columns directly to the leaf nodes of a B-Tree index. This allows PostgreSQL to perform an **Index Only Scan**, reading column data directly from the index without fetching the actual row heap.

```sql
CREATE INDEX idx_users_lookup 
    ON users (email) 
    INCLUDE (username, first_name);
    
-- The following query can be resolved entirely within the index tree:
SELECT username, first_name FROM users WHERE email = 'john@example.com';
```

---

## 3. Non-Blocking Index Creation

Standard index creation locks table writes (`INSERT`/`UPDATE`/`DELETE`). In production environments, use `CONCURRENTLY` to build the index without blocking queries.

```sql
CREATE INDEX CONCURRENTLY idx_users_email ON users (email);
```
> [!WARNING]
> - `CREATE INDEX CONCURRENTLY` cannot be wrapped inside transactional blocks (`BEGIN...COMMIT`).
> - The build takes longer because it performs a two-pass table scan.
> - If the build fails (e.g. due to constraint violations), it leaves an "invalid" index. Use `DROP INDEX CONCURRENTLY` and rebuild.

---

## 4. Query Analysis with EXPLAIN ANALYZE

Use `EXPLAIN` to view the query planner's execution path. Append `ANALYZE` to execute the query and report actual execution times alongside estimations.

```sql
EXPLAIN (ANALYZE, BUFFERS)
SELECT * FROM users WHERE email = 'john@example.com';
```
- **`ANALYZE`**: Runs the query and collects actual execution times.
- **`BUFFERS`**: Displays shared memory block hits, reads, and writes (crucial for IO profiling).

### Understanding PostgreSQL Scan Node Types

- **`Seq Scan`**: Sequential Scan. Scans the entire table disk page by page. Common on small tables or columns lacking index support.
- **`Index Scan`**: Navigates the index tree to find match IDs, then fetches matching pages from the main table heap one by one.
- **`Index Only Scan`**: Retrieves all requested values directly from the index leaf nodes. Avoids accessing the main table heap completely.
- **`Bitmap Index Scan` + `Bitmap Heap Scan`**: 
  - **`Bitmap Index Scan`**: Scans the index first and builds a 2D bitmap of matching disk page locations in memory.
  - **`Bitmap Heap Scan`**: Reads the bitmap, sorting physical disk page lookups sequentially. This avoids random disk seek operations, transforming random IO into efficient sequential page reads.
