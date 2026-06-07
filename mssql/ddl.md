## 1. Schema & Database Management

### Database Creation

```sql
CREATE DATABASE SalesDB;
GO -- GO is the client batch separator in SQL Server
```

### Schemas

In SQL Server, schemas are logical namespaces that separate tables, views, and procedures. Objects are addressed using the `SchemaName.ObjectName` format.

```sql
-- 1. Create a schema owned by dbo (database owner)
CREATE SCHEMA sales AUTHORIZATION dbo;
GO

-- 2. Create a table inside the sales schema
CREATE TABLE sales.customers (
    id INT IDENTITY(1,1) PRIMARY KEY,
    name NVARCHAR(100) NOT NULL
);
```

---

## 2. Table Creation & Identity Columns

SQL Server uses the `IDENTITY(seed, increment)` attribute to generate auto-incrementing integers. Only one identity column is allowed per table.

```sql
CREATE TABLE sales.orders (
    order_id INT IDENTITY(1,1) PRIMARY KEY, -- Starts at 1, increments by 1
    customer_id INT NOT NULL,
    order_date DATE DEFAULT GETDATE(),
    status VARCHAR(20) DEFAULT 'Pending',

    -- Table level constraints
    CONSTRAINT fk_orders_customer FOREIGN KEY (customer_id)
        REFERENCES sales.customers(id)
        ON DELETE CASCADE
        ON UPDATE NO ACTION
);
```

---

## 3. Temporary Tables & Table Variables

SQL Server supports three formats for temporary storage.

### 1. Local Temporary Tables (`#table`)

Stored in the `tempdb` database. Visible only to the current connection/session; automatically dropped when the session closes.

```sql
CREATE TABLE #TempReport (
    user_id INT,
    total_sales DECIMAL(10,2)
);

INSERT INTO #TempReport VALUES (1, 500.50);
-- Accessible as select * from #TempReport;
```

### 2. Global Temporary Tables (`##table`)

Visible to all active database connections. Dropped automatically when the session that created it disconnects and all other sessions stop referencing it.

```sql
CREATE TABLE ##GlobalCache (
    cache_key VARCHAR(50) PRIMARY KEY,
    cache_value NVARCHAR(MAX)
);
```

### 3. Table Variables (`@table`)

Declared like standard variables. They live in memory (but can spill to tempdb) and are scoped to the current batch of execution.

- **Transactional Behavior**: Table variables are not bound to active transactions. If the transaction rolls back, data inserted into a table variable remains!

```sql
DECLARE @ProductList TABLE (
    product_id INT PRIMARY KEY,
    price DECIMAL(10,2)
);

INSERT INTO @ProductList VALUES (101, 19.99);
-- Must be queried in the exact same execution batch
SELECT * FROM @ProductList;
```

---

## 4. Constraints

### Primary and Foreign Key Additions

```sql
-- Add Primary Key to existing table
ALTER TABLE sales.orders
    ADD CONSTRAINT pk_orders PRIMARY KEY (order_id);

-- Add Foreign Key with cascading deletion
ALTER TABLE sales.orders
    ADD CONSTRAINT fk_orders_customer
    FOREIGN KEY (customer_id) REFERENCES sales.customers (id)
    ON DELETE CASCADE;
```

### Check Constraints

```sql
ALTER TABLE sales.orders
    ADD CONSTRAINT chk_status_types
    CHECK (status IN ('Pending', 'Processing', 'Shipped', 'Cancelled'));
```

### Dropping Constraints

```sql
-- In SQL Server, you drop constraints by their name directly
ALTER TABLE sales.orders DROP CONSTRAINT fk_orders_customer;
```

---

## 5. Alter Table Operations

```sql
-- 1. Add column
ALTER TABLE sales.customers ADD phone_number VARCHAR(20) NULL;

-- 2. Modify column data type (requires ALTER COLUMN)
ALTER TABLE sales.customers ALTER COLUMN phone_number VARCHAR(50) NOT NULL;

-- 3. Drop column
ALTER TABLE sales.customers DROP COLUMN phone_number;
```

---

## 6. Truncate vs. Delete

`TRUNCATE TABLE` is a DDL command that resets the `IDENTITY` counter and releases storage allocations.

```sql
-- Empties table data, resets identity seeds to original definitions (e.g. 1)
TRUNCATE TABLE sales.orders;
```

| Feature                | TRUNCATE TABLE                     | DELETE FROM                          |
| :--------------------- | :--------------------------------- | :----------------------------------- |
| **Category**           | DDL                                | DML                                  |
| **Speed**              | Extremely Fast (deallocates pages) | Slower (deletes row by row)          |
| **Transaction log**    | Minimally logged                   | Fully logged                         |
| **Identity Reset**     | Resets `IDENTITY`                  | Does NOT reset `IDENTITY`            |
| **Trigger Activation** | Does not fire triggers             | Fires `DELETE` triggers              |
| **Foreign Keys**       | Cannot execute if referenced by FK | Allowed (will cascade if configured) |
