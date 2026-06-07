## 1. Transaction Syntax & Savepoints

```sql
BEGIN TRANSACTION; -- Or: BEGIN TRAN;

-- Perform operations
UPDATE sales.accounts SET balance = balance - 100.00 WHERE id = 1;

-- Create savepoint for partial rollback
SAVE TRANSACTION CheckpointA;

-- Attempt audit log
INSERT INTO sales.logs (event) VALUES ('Transferred funds');

-- If something fails within this block:
-- ROLLBACK TRANSACTION CheckpointA;

-- Savepoints do not need to be released explicitly.
-- Commit all operations
COMMIT TRANSACTION; -- Or: COMMIT TRAN;
```

---

## 2. Transaction Isolation Levels

To set the isolation level for the current session:

```sql
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
```

### Supported Isolation Levels

- **`READ UNCOMMITTED`**: Allows dirty reads (reading uncommitted data). Also requested per table using the `NOLOCK` hint.
- **`READ COMMITTED`**: **SQL Server Default.** Uses shared locks to prevent dirty reads. Locks are released immediately after the statement completes.
- **`REPEATABLE READ`**: Holds locks on read rows until the transaction ends. Prevents non-repeatable reads.
- **`SERIALIZABLE`**: Places range locks on indexes to prevent phantom reads.
- **`SNAPSHOT`**: Uses row versioning in `tempdb`. Readers do not block writers, and writers do not block readers. Reads see the data snapshot taken at the start of the _transaction_.
  - _Note_: Requires database configuration:
    ```sql
    ALTER DATABASE SalesDB SET ALLOW_SNAPSHOT_ISOLATION ON;
    ```
- **Read Committed Snapshot Isolation (RCSI)**: Changes the behavior of standard `READ COMMITTED` to use row versioning instead of locks. Reads see the data snapshot at the start of the _statement_.
  - _Note_: Requires database configuration:
    ```sql
    ALTER DATABASE SalesDB SET READ_COMMITTED_SNAPSHOT ON;
    ```

---

## 3. Locking Hints (Table Hints)

T-SQL allows querying rows with explicit locking overrides appended to table names.

```sql
SELECT * FROM sales.inventory WITH (LOCK_HINT) WHERE item_id = 101;
```

### Common Locking Hints

- **`NOLOCK`**: Performs a dirty read (equivalent to `READ UNCOMMITTED`). Prevents queries from blocking on active table locks.
- **`ROWLOCK`**: Forces row-level locks instead of page-level or table-level locks.
- **`UPDLOCK`**: Takes update locks instead of shared locks while reading. Prevents other transactions from updating the row until this transaction completes (prevents deadlocks).
- **`XLOCK`**: Takes exclusive locks on the read rows (blocks all reads and writes from other sessions).
- **`HOLDLOCK`**: Holds a shared lock until the transaction ends (equivalent to `SERIALIZABLE`).

```sql
-- Read inventory with an update lock to prevent concurrent modifications
BEGIN TRAN;
SELECT stock_count
FROM sales.inventory WITH (UPDLOCK, ROWLOCK)
WHERE product_id = 45;

-- Update stock
UPDATE sales.inventory SET stock_count = stock_count - 1 WHERE product_id = 45;
COMMIT TRAN;
```

---

## 4. Transactional DDL

SQL Server supports running DDL statements within active transactions.

```sql
BEGIN TRAN;

-- Create table
CREATE TABLE sales.temp_discounts (id INT, pct INT);

-- Add column
ALTER TABLE sales.customers ADD discount_group INT;

-- Rollback recovers original database schema state
ROLLBACK TRAN;
```

---

## 5. Structured Exception Handling (TRY...CATCH)

Inside stored procedures, wrap transactions in a `TRY...CATCH` block. Use **`XACT_STATE()`** to verify if a transaction has hit a fatal error and is uncommittable (which requires a rollback).

```sql
BEGIN TRY
    BEGIN TRANSACTION;

    -- Modification statements
    UPDATE sales.accounts SET balance = balance - 100 WHERE id = 1;
    UPDATE sales.accounts SET balance = balance + 100 WHERE id = 2;

    COMMIT TRANSACTION;
END TRY
BEGIN CATCH
    -- Check if transaction is active and committable
    IF XACT_STATE() = 1
    BEGIN
        COMMIT TRANSACTION;
    END;

    -- Check if transaction is doomed (must rollback)
    IF XACT_STATE() = -1
    BEGIN
        ROLLBACK TRANSACTION;
    END;

    -- Raise the error again
    THROW;
END CATCH;
```

- **`XACT_STATE() = 1`**: Active transaction, can be committed.
- **`XACT_STATE() = -1`**: Active transaction, but an error has doomed it. It cannot be committed; only rollback is allowed.
- **`XACT_STATE() = 0`**: No active transaction in the current session.
