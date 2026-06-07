## 1. Functions vs Procedures

PostgreSQL distinguishes between functions (which compute values) and procedures (which execute commands and support transaction control).

### Functions (`CREATE FUNCTION`)

Functions run inside the calling transaction block and cannot commit or roll back. They can be embedded inside standard `SELECT` expressions.

```sql
CREATE OR REPLACE FUNCTION get_user_tier(p_total_spent NUMERIC)
RETURNS VARCHAR
LANGUAGE plpgsql
AS $$
DECLARE
    v_tier VARCHAR;
BEGIN
    IF p_total_spent >= 5000.00 THEN
        v_tier := 'PLATINUM';
    ELSIF p_total_spent >= 1000.00 THEN
        v_tier := 'GOLD';
    ELSE
        v_tier := 'BRONZE';
    END IF;

    RETURN v_tier;
END;
$$;
```

#### Return Table Function Example

```sql
CREATE OR REPLACE FUNCTION get_active_members(p_min_spent NUMERIC)
RETURNS TABLE (user_id INT, username TEXT, spent NUMERIC)
LANGUAGE plpgsql
AS $$
BEGIN
    RETURN QUERY
    SELECT id, name, total_spent
    FROM members
    WHERE is_active = TRUE AND total_spent >= p_min_spent;
END;
$$;
```

### Procedures (`CREATE PROCEDURE`)

Procedures support transaction controls (`COMMIT`/`ROLLBACK`) inside their body. They cannot be executed inside `SELECT` queries and must be invoked using `CALL`.

```sql
CREATE OR REPLACE PROCEDURE process_batch_payments()
LANGUAGE plpgsql
AS $$
DECLARE
    r RECORD;
BEGIN
    FOR r IN SELECT id, amount FROM pending_payouts LOOP
        -- Process payout logic
        UPDATE accounts SET balance = balance - r.amount WHERE id = r.id;

        -- Commit changes after processing each row individually!
        COMMIT;
    END LOOP;
END;
$$;
```

To run:

```sql
CALL process_batch_payments();
```

---

## 2. Control Flow Syntax

Control flow operations are written inside procedural blocks ($$\dots$$).

### Conditional (IF / CASE)

```sql
-- IF-THEN-ELSIF
IF condition THEN
    -- statements
ELSIF other_condition THEN
    -- statements
ELSE
    -- statements
END IF;

-- CASE
CASE variable
    WHEN val1, val2 THEN
        -- statements
    ELSE
        -- statements
END CASE;
```

### Loops

#### FOR (Numeric Range)

```sql
FOR i IN 1..10 LOOP
    -- statements (i is implicitly declared as integer)
END LOOP;
```

#### FOR (Query Results)

Loops through matching rows of a query.

```sql
DECLARE
    r RECORD;
BEGIN
    FOR r IN SELECT id, email FROM users WHERE is_active = TRUE LOOP
        -- Access fields using r.id, r.email
    END LOOP;
END;
```

#### WHILE

```sql
WHILE counter < 10 LOOP
    counter := counter + 1;
END LOOP;
```

---

## 3. Triggers & Trigger Functions

PostgreSQL processes triggers in two steps:

1. Define a **Trigger Function** returning type `TRIGGER`.
2. Bind that trigger function to a target table.

```sql
-- 1. Create the trigger function
CREATE OR REPLACE FUNCTION log_user_changes()
RETURNS TRIGGER
LANGUAGE plpgsql
AS $$
BEGIN
    -- TG_OP stores the triggering action ('INSERT', 'UPDATE', 'DELETE')
    IF TG_OP = 'UPDATE' THEN
        INSERT INTO audit_logs (table_name, record_id, action, old_data, new_data)
        VALUES ('users', OLD.id, 'UPDATE', to_jsonb(OLD), to_jsonb(NEW));
    ELSIF TG_OP = 'DELETE' THEN
        INSERT INTO audit_logs (table_name, record_id, action, old_data)
        VALUES ('users', OLD.id, 'DELETE', to_jsonb(OLD));
    END IF;

    RETURN NEW; -- Returns proposed row for inserts/updates
END;
$$;

-- 2. Bind the trigger to the table
CREATE TRIGGER trg_user_audit
AFTER UPDATE OR DELETE ON users
FOR EACH ROW
EXECUTE FUNCTION log_user_changes();
```

### Special Variables in Trigger Functions

- **`NEW`**: Data row proposed for insertion or updated values (record type).
- **`OLD`**: Original data row before update or delete operations (record type).
- **`TG_OP`**: Trigger event name (`INSERT`, `UPDATE`, `DELETE`, `TRUNCATE`).
- **`TG_TABLE_NAME`**: Name of the table triggering the execution.

---

## 4. Exception Handling

Use the `EXCEPTION` block to intercept database errors and resolve them programmatically.

```sql
CREATE OR REPLACE FUNCTION safe_division(numerator NUMERIC, denominator NUMERIC)
RETURNS NUMERIC
LANGUAGE plpgsql
AS $$
DECLARE
    result NUMERIC;
BEGIN
    result := numerator / denominator;
    RETURN result;

EXCEPTION
    -- Catch division by zero errors
    WHEN division_by_zero THEN
        RAISE WARNING 'Attempted to divide by zero. Returning NULL.';
        RETURN NULL;

    -- Catch all other exceptions
    WHEN OTHERS THEN
        RAISE EXCEPTION 'An unexpected error occurred: %', SQLERRM;
END;
$$;
```

### Raising Custom Errors

```sql
RAISE EXCEPTION 'Invalid age value: %', p_age
    USING ERRCODE = 'invalid_parameter_value';
```

- **`SQLERRM`**: Global variable storing the text description of the current error.
- **`SQLSTATE`**: Global variable storing the 5-character SQL standard error code.
- **`RAISE LEVEL`**: Levels include `DEBUG`, `LOG`, `INFO`, `NOTICE`, `WARNING`, `EXCEPTION` (aborts transaction).
