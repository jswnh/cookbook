# MySQL Data Definition Language (DDL) Syntax

DDL commands are used to define, modify, and manage database structures (databases, tables, views, indexes, schemas). In MySQL, most DDL statements commit transactions implicitly and cannot be rolled back.

---

## 1. Database Operations

### Create Database / Schema
In MySQL, `SCHEMA` is a synonym for `DATABASE`.

```sql
-- Create with default character set and collation
CREATE DATABASE IF NOT EXISTS app_db
    CHARACTER SET utf8mb4
    COLLATE utf8mb4_0900_ai_ci;
```

### Alter Database
```sql
ALTER DATABASE app_db
    CHARACTER SET utf8mb4
    COLLATE utf8mb4_bin;
```

### Drop Database
```sql
DROP DATABASE IF EXISTS app_db;
```

---

## 2. Table Operations

### Create Table Syntax
```sql
CREATE TABLE IF NOT EXISTS users (
    id BIGINT UNSIGNED AUTO_INCREMENT,
    email VARCHAR(255) NOT NULL,
    password_hash CHAR(60) NOT NULL,
    nickname VARCHAR(50),
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    -- Table level constraints
    CONSTRAINT pk_users PRIMARY KEY (id),
    CONSTRAINT uq_user_email UNIQUE (email)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;
```

### Temporary Tables
Visible only to the current session and automatically dropped when the session closes.
```sql
CREATE TEMPORARY TABLE temp_report (
    id INT,
    summary VARCHAR(100)
);
```

### Create Table Copy Variants
```sql
-- 1. Create table with the exact same structure (including indexes and auto_increment settings)
CREATE TABLE users_archive LIKE users;

-- 2. Create table based on query results (does NOT copy primary keys or indexes)
CREATE TABLE premium_users AS 
SELECT id, email, created_at 
FROM users 
WHERE is_active = TRUE;
```

---

## 3. Constraints

### Primary Key
Enforces unique, non-null values. Only one Primary Key is allowed per table.
```sql
ALTER TABLE users ADD CONSTRAINT pk_users PRIMARY KEY (id);
```

### Foreign Key
Ensures referential integrity. MySQL requires that referenced tables and columns are indexed.
```sql
CREATE TABLE orders (
    id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    user_id BIGINT UNSIGNED NOT NULL,
    order_date DATE,
    
    CONSTRAINT fk_orders_user_id 
        FOREIGN KEY (user_id) REFERENCES users (id)
        ON DELETE CASCADE
        ON UPDATE RESTRICT
);
```
Actions supported: `RESTRICT`, `CASCADE`, `SET NULL`, `NO ACTION`, `SET DEFAULT` (not supported by InnoDB).

### Check Constraint
Evaluates boolean expressions before inserting/updating. Available in MySQL 8.0.16+.
```sql
CREATE TABLE products (
    id INT PRIMARY KEY,
    price DECIMAL(10, 2),
    stock INT,
    
    CONSTRAINT chk_positive_price CHECK (price > 0.00),
    CONSTRAINT chk_min_stock CHECK (stock >= 0)
);
```

### Drop Constraints
```sql
-- Drop Foreign Key (requires key symbol name, not column name)
ALTER TABLE orders DROP FOREIGN KEY fk_orders_user_id;

-- Drop Unique or Index
ALTER TABLE users DROP INDEX uq_user_email;

-- Drop Check Constraint
ALTER TABLE products DROP CHECK chk_positive_price;
```

---

## 4. Alter Table Operations

InnoDB supports Online DDL which allows queries to run concurrently while a table is modified.

```sql
-- Add column
ALTER TABLE users 
    ADD COLUMN phone_number VARCHAR(20) DEFAULT NULL AFTER nickname;

-- Modify column type/attributes
ALTER TABLE users 
    MODIFY COLUMN nickname VARCHAR(100) NOT NULL;

-- Rename column
ALTER TABLE users 
    RENAME COLUMN nickname TO display_name;

-- Drop column
ALTER TABLE users 
    DROP COLUMN phone_number;

-- Rename entire table
ALTER TABLE users RENAME TO system_users;
```

### Online DDL Specification
You can explicitly define the algorithm and lock settings:
```sql
ALTER TABLE users 
    ADD COLUMN secondary_email VARCHAR(255) DEFAULT NULL,
    ALGORITHM=INPLACE, 
    LOCK=NONE;
```
- **Algorithms**: `COPY`, `INPLACE`, `INSTANT` (added in 8.0).
- **Locks**: `DEFAULT`, `NONE`, `SHARED`, `EXCLUSIVE`.

---

## 5. Truncate vs Drop vs Delete

| Command | Category | Transactional | Speed | Behaviour |
| :--- | :--- | :--- | :--- | :--- |
| **`DROP TABLE`** | DDL | No (Auto-commits) | Fast | Deletes table schema, metadata, indexes, and data. |
| **`TRUNCATE TABLE`** | DDL | No (Auto-commits) | Very Fast | Drops and re-creates table. Resets `AUTO_INCREMENT` values. |
| **`DELETE FROM`** | DML | Yes (Can Rollback) | Slow | Removes rows one by one. Does not reset `AUTO_INCREMENT`. |

```sql
-- Drop table completely
DROP TABLE IF EXISTS users_archive;

-- Clear all data instantly and reset auto_increment counter
TRUNCATE TABLE premium_users;
```

---

## 6. Table Partitioning

MySQL supports partitioning tables into logical parts based on column ranges, list mappings, hash divisions, or keys.

### Range Partitioning
Useful for chronological data.
```sql
CREATE TABLE transaction_logs (
    id INT NOT NULL,
    amount DECIMAL(10,2),
    log_date DATE NOT NULL
)
PARTITION BY RANGE (YEAR(log_date)) (
    PARTITION p2024 VALUES LESS THAN (2025),
    PARTITION p2025 VALUES LESS THAN (2026),
    PARTITION p2026 VALUES LESS THAN (2027),
    PARTITION p_future VALUES LESS THAN MAXVALUE
);
```

### Hash Partitioning
Distributes data evenly across a specific number of partitions.
```sql
CREATE TABLE customer_sessions (
    session_id INT NOT NULL PRIMARY KEY,
    user_id INT,
    login_time TIMESTAMP
)
PARTITION BY HASH(session_id)
PARTITIONS 4;
```
