# MS SQL Server Indexes & Query Optimization

Indexes are critical B-Tree (and Columnstore) structures that speed up data retrieval. SQL Server organizes tables as either Clustered Index tables (physically sorted by index key) or Heaps (unordered tables).

---

## 1. Index Types

### Clustered Index
A table can have exactly one clustered index. The leaf pages of a clustered index contain the actual data rows of the table.

```sql
CREATE CLUSTERED INDEX idx_customers_lastname 
    ON sales.customers (last_name);
```

### Nonclustered Index
Stored separately from the table data. The leaf pages contain index keys and a pointer (the clustering key or physical row ID) back to the table data.

```sql
CREATE NONCLUSTERED INDEX idx_customers_email 
    ON sales.customers (email);
```

### Columnstore Indexes (OLAP Optimization)
Instead of row-by-row storage, Columnstore indexes organize data in columns. Highly optimized for large-scale data warehousing aggregates.
- **Clustered Columnstore**: The entire table is compressed and stored as columns.
- **Nonclustered Columnstore**: A read-only analytical copy of a rowstore table.

```sql
-- Create Clustered Columnstore Index on a data warehouse table
CREATE CLUSTERED COLUMNSTORE INDEX ccsi_financial_records 
    ON sales.financial_records;
```

---

## 2. Advanced Indexing Strategies

### Included Columns (`INCLUDE` Clause)
Appends non-key columns directly to the leaf pages of a nonclustered index. This allows the query engine to perform an **Index Seek** followed by an **Index-Only Scan**, returning values directly from the index without doing a costly key lookup in the clustered index.

```sql
CREATE NONCLUSTERED INDEX idx_orders_customer_lookup
    ON sales.orders (customer_id)
    INCLUDE (order_date, total_amount);
    
-- Covered Query (requires no Key Lookup)
SELECT order_date, total_amount 
FROM sales.orders 
WHERE customer_id = 42;
```

### Filtered Indexes
Indexes only a subset of rows that match a `WHERE` condition. Saves storage and speeds up writes.

```sql
-- Index only active subscriptions
CREATE NONCLUSTERED INDEX idx_active_subscriptions 
    ON sales.subscriptions (email) 
    WHERE status = 'Active';
```

---

## 3. Index Maintenance

As rows are updated, indexes become fragmented. SQL Server provides two commands to resolve fragmentation:

- **`REORGANIZE`**: In-place clean up. Online operation, minimal resource usage. Recommended for minor fragmentation (5% to 30%).
- **`REBUILD`**: Creates a fresh copy of the index. Can be run `ONLINE = ON` in Enterprise edition. Recommended for major fragmentation (>30%).

```sql
-- 1. Reorganize index
ALTER INDEX idx_customers_email ON sales.customers REORGANIZE;

-- 2. Rebuild index online
ALTER INDEX idx_customers_email ON sales.customers 
    REBUILD WITH (ONLINE = ON);
```

---

## 4. Query Profiling & Execution Plans

Use these T-SQL session commands to analyze query performance:

```sql
-- 1. Show logical and physical disk reads (crucial for performance assessment)
SET STATISTICS IO ON;

-- 2. Show execution times
SET STATISTICS TIME ON;

-- Run query
SELECT * FROM sales.customers WHERE last_name = 'Smith';

-- Turn off statistics once done
SET STATISTICS IO, TIME OFF;
```

### Key Terms in STATISTICS IO Output
- **Logical Reads**: The number of 8KB database pages read from the buffer cache. **This is the key metric to optimize.**
- **Physical Reads**: The number of pages read directly from disk.

### Key Index Scan Operators
When reviewing execution plans, watch out for these operations:
- **`Table Scan`**: Scans the entire table (Heap table lacking clustered index). Bad for large tables.
- **`Clustered Index Scan`**: Scans the entire clustered index (equivalent to a full table scan). Bad.
- **`Index Seek`**: Narrows down search range using index navigation. Excellent.
- **`Key Lookup (Clustered)`**: The index returned row addresses, but the query selected columns not covered by the index, forcing SQL Server to fetch the full row from the clustered index. **Optimize this by adding `INCLUDE` columns.**
- **`Index Spool`**: The optimizer generated a temporary index in `tempdb` during query execution. Indicates a missing index.
