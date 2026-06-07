# PostgreSQL Transactions & Concurrency Reference

PostgreSQL supports fully ACID-compliant transactions. It has a robust Multiversion Concurrency Control (MVCC) engine that minimizes reader-writer blocking.

---

## 1. Transaction Control & Savepoints

```sql
BEGIN; -- Or: START TRANSACTION;

-- Perform operations
UPDATE accounts SET balance = balance - 100.00 WHERE id = 1;

-- Create a partial rollback point
SAVEPOINT my_savepoint;

-- Attempt an operation
INSERT INTO log (message) VALUES ('Transferred money');

-- If something failed inside the savepoint block:
-- ROLLBACK TO SAVEPOINT my_savepoint;

-- Release savepoint resources once done
RELEASE SAVEPOINT my_savepoint;

-- Make transaction permanent
COMMIT; -- Or: END;

-- Or undo all statements in the transaction
-- ROLLBACK;
```

---

## 2. Transaction Isolation Levels

To set the isolation level:
```sql
-- For the current transaction block
BEGIN TRANSACTION ISOLATION LEVEL REPEATABLE READ;

-- For the entire session
SET SESSION CHARACTERISTICS AS TRANSACTION ISOLATION LEVEL SERIALIZABLE;
```

### Isolation Levels Comparison in PostgreSQL

| Isolation Level | Dirty Reads | Non-Repeatable Reads | Phantom Reads | Serialization Anomalies |
| :--- | :--- | :--- | :--- | :--- |
| **`READ UNCOMMITTED`** | **No** | Yes | Yes | Yes |
| **`READ COMMITTED`** | No | Yes | Yes | Yes |
| **`REPEATABLE READ`** | No | **No** | **No** | Yes (Write Skew is possible) |
| **`SERIALIZABLE`** | No | No | No | **No** (Fully isolated) |

> [!IMPORTANT]
> - **No Dirty Reads in PG**: In PostgreSQL, `READ UNCOMMITTED` behaves exactly like `READ COMMITTED` because the storage engine does not allow reading uncommitted data.
> - **Serializable Snapshot Isolation (SSI)**: PostgreSQL's `SERIALIZABLE` level uses SSI to monitor locks and detect write-skew anomalies, automatically aborting conflicting transactions with a `40001` serialization failure (requiring the application to retry the transaction).

---

## 3. Explicit Row-Level Locks

PostgreSQL provides highly granular row-level locks that can be requested inside transactional blocks.

```sql
SELECT * FROM table FOR [ LOCK_STRENGTH ] [ NOWAIT | SKIP LOCKED ];
```

### Lock Strengths
1. **`FOR UPDATE`**: Standard exclusive lock. Blocks others from updating, deleting, or acquiring any lock.
2. **`FOR NO KEY UPDATE`**: Similar to `FOR UPDATE` but does not block shared key locks (`FOR KEY SHARE`). Used when updating columns that are *not* part of any foreign keys or unique constraints (highly optimizes locking overhead).
3. **`FOR SHARE`**: Shared lock. Allows others to read and acquire shared locks, but prevents updates/deletes.
4. **`FOR KEY SHARE`**: Weakest lock. Allows others to execute `FOR NO KEY UPDATE` or read, but blocks deletes or updates of the primary/unique key columns.

### Non-Blocking Modifiers
- **`NOWAIT`**: Fails immediately if the row is locked.
- **`SKIP LOCKED`**: Skips locked rows.
```sql
-- Fetch the next job, skip items locked by other concurrent processes
SELECT * FROM active_jobs 
WHERE status = 'queued' 
LIMIT 1 
FOR UPDATE SKIP LOCKED;
```

---

## 4. Transactional DDL

Unlike MySQL, PostgreSQL can wrap schema changes (DDL statements like `CREATE TABLE`, `ALTER TABLE`, `DROP TABLE`) in transactions.

```sql
BEGIN;

-- Create table
CREATE TABLE customer_rewards (id INT, points INT);

-- Add column
ALTER TABLE users ADD COLUMN rewards_id INT;

-- Create constraint linking them
ALTER TABLE users 
    ADD CONSTRAINT fk_users_rewards 
    FOREIGN KEY (rewards_id) REFERENCES customer_rewards(id);

-- If everything works fine, commit.
-- If any of the above fails, standard ROLLBACK will undo all schema changes!
COMMIT;
```
> [!WARNING]
> Certain administrative operations cannot run inside a transaction, such as `VACUUM`, `CREATE DATABASE`, `REINDEX DATABASE`, and `CREATE INDEX CONCURRENTLY`.

---

## 5. Advisory Locks

Advisory locks are application-defined locks stored in the database's shared memory. PostgreSQL does not enforce them automatically; the application must explicitly acquire and release them. Highly useful for coordinating application tasks (like background workers).

- **Session-level**: Lock is held until the session closes or is explicitly unlocked.
- **Transaction-level**: Lock is automatically released when the transaction completes.

```sql
-- 1. Acquire transaction-level exclusive advisory lock (blocking)
-- Passing a 64-bit integer key
SELECT pg_advisory_xact_lock(450892);

-- 2. Non-blocking attempt to acquire a session-level advisory lock
-- Returns TRUE if successful, FALSE if already locked by another session
SELECT pg_try_advisory_lock(992011);

-- 3. Release session-level advisory lock (required if acquired successfully)
SELECT pg_advisory_unlock(992011);
```
- **`pg_advisory_xact_lock(key)`**: Automatically released on `COMMIT`/`ROLLBACK`.
- **`pg_advisory_unlock(key)`**: Releases a session-level lock manually.
