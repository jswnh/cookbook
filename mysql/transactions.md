## 1. Basic Transaction Syntax

```sql
-- Disable automatic commit for the current session (optional)
SET autocommit = 0;

-- Start a new transaction
START TRANSACTION;
-- Or: BEGIN;

-- Perform operations
UPDATE accounts SET balance = balance - 100.00 WHERE account_id = 1;
UPDATE accounts SET balance = balance + 100.00 WHERE account_id = 2;

-- Check and commit to make changes permanent
COMMIT;

-- Or abort and discard all modifications
ROLLBACK;
```

---

## 2. Savepoints (Partial Rollbacks)

Savepoints allow you to roll back sections of a transaction without canceling the entire sequence of operations.

```sql
START TRANSACTION;

-- Operation A
INSERT INTO log_table (event) VALUES ('Starting job');
SAVEPOINT checkpoint_a;

-- Operation B
INSERT INTO data_table (id, val) VALUES (1, 'A');
-- Oh no, an error! Let's roll back ONLY to checkpoint_a
ROLLBACK TO SAVEPOINT checkpoint_a;

-- Operation C
INSERT INTO data_table (id, val) VALUES (2, 'B');

-- Release the savepoint memory (optional)
RELEASE SAVEPOINT checkpoint_a;

-- Commit Operation A and C
COMMIT;
```

---

## 3. Transaction Isolation Levels

Isolation levels dictate how transaction operations are visible to other concurrent sessions.

### Setting Isolation Levels

```sql
-- Set isolation level for the next transaction in the session
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;

-- Set globally (affects new connections)
SET GLOBAL TRANSACTION ISOLATION LEVEL SERIALIZABLE;
```

### Supported Isolation Levels

| Isolation Level        | Dirty Reads | Non-Repeatable Reads | Phantom Reads                  | Notes                                                                                               |
| :--------------------- | :---------- | :------------------- | :----------------------------- | :-------------------------------------------------------------------------------------------------- |
| **`READ UNCOMMITTED`** | Yes         | Yes                  | Yes                            | Lowest isolation. Reads uncommitted changes (dirty reads).                                          |
| **`READ COMMITTED`**   | No          | Yes                  | Yes                            | Reads only committed data. Consistent reads read fresh snapshots.                                   |
| **`REPEATABLE READ`**  | No          | No                   | No (via MVCC / Next-key locks) | **MySQL default**. Consistent reads read the snapshot taken by the _first_ read in the transaction. |
| **`SERIALIZABLE`**     | No          | No                   | No                             | Highest isolation. Converts all plain `SELECT` statements to `SELECT ... FOR SHARE`.                |

---

## 4. Explicit Locking in Selects

MySQL 8.0+ supports advanced explicit row-level locking flags. These are used inside transactional blocks to prevent concurrent modifications.

### Shared Lock (`FOR SHARE`)

Allows other sessions to read the rows but blocks them from modifying or deleting.

```sql
START TRANSACTION;
SELECT * FROM products WHERE category = 'electronics' FOR SHARE;
-- Other sessions can read, but updates are queued until this transaction commits.
COMMIT;
```

> [!NOTE]
> `LOCK IN SHARE MODE` is the legacy MySQL 5.x syntax and is still supported for backwards compatibility but `FOR SHARE` is preferred.

### Exclusive Lock (`FOR UPDATE`)

Blocks other sessions from reading with lock, updating, or deleting the matching rows.

```sql
START TRANSACTION;
SELECT * FROM inventory WHERE item_id = 101 FOR UPDATE;
-- Operations modifying stock
UPDATE inventory SET stock = stock - 1 WHERE item_id = 101;
COMMIT;
```

### Concurrency Modifiers (`NOWAIT` & `SKIP LOCKED`)

Added in MySQL 8.0, these modifiers prevent transactions from blocking indefinitely on locked rows.

- **`NOWAIT`**: Fails immediately with an error if target rows are already locked by another session.

```sql
SELECT * FROM bookings WHERE seat_no = 'A12' FOR UPDATE NOWAIT;
-- Error 3572: Statement aborted because lock waiting time exceeded.
```

- **`SKIP LOCKED`**: Skips any locked rows and returns only unlocked rows. Extremely useful for implementing high-throughput queue systems.

```sql
-- Fetch the first available job that isn't currently locked by another worker
SELECT * FROM jobs
WHERE status = 'pending'
LIMIT 1
FOR UPDATE SKIP LOCKED;
```

---

## 5. Implicit Commits (DDL Triggered)

Some SQL statements cannot be rolled back and immediately commit any active transaction automatically. This is known as **implicit commit**.

Common statements triggering implicit commits:

- **DDL Statements**: `CREATE TABLE`, `ALTER TABLE`, `DROP TABLE`, `RENAME TABLE`, `TRUNCATE TABLE`, etc.
- **Database operations**: `CREATE DATABASE`, `DROP DATABASE`, `ALTER DATABASE`.
- **Administrative/User Operations**: `CREATE USER`, `DROP USER`, `GRANT`, `REVOKE`.
- **Transaction Commands**: Beginning a new transaction with `START TRANSACTION` before committing the current one.
- **Locking commands**: `LOCK TABLES`, `UNLOCK TABLES`.

### Implicit Commit Example

```sql
START TRANSACTION;
INSERT INTO logs (msg) VALUES ('Before table creation');

-- This statement causes an IMPLICIT COMMIT!
CREATE TABLE temp_markers (id INT);

-- The log insert above is now committed to the database.
-- The ROLLBACK below will ONLY rollback statements executed AFTER the CREATE TABLE.
ROLLBACK;
```

---

## 6. Autocommit Mode

By default, MySQL runs with autocommit enabled. Every SQL statement that modifies data is executed in its own single-statement transaction and committed automatically.

```sql
-- Check current autocommit status
SELECT @@autocommit; -- Returns 1 (Enabled) or 0 (Disabled)

-- Disable autocommit
SET autocommit = OFF;
-- Or: SET autocommit = 0;

-- Now you must explicitly call COMMIT or ROLLBACK.
-- Re-enable autocommit:
SET autocommit = ON;
```
