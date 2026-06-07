## 1. Stored Procedures

Procedures execute sets of SQL statements and can return parameters, but cannot be directly embedded inside other SELECT queries.

### Creating Stored Procedures

```sql
DELIMITER //

CREATE PROCEDURE GetUserStats(
    IN  p_user_id INT,
    OUT p_order_count INT,
    OUT p_total_spent DECIMAL(10,2)
)
BEGIN
    -- Initialize variables
    SET p_order_count = 0;
    SET p_total_spent = 0.00;

    -- Query values into output variables
    SELECT COUNT(*), COALESCE(SUM(amount), 0.00)
    INTO p_order_count, p_total_spent
    FROM orders
    WHERE user_id = p_user_id;
END //

DELIMITER ;
```

> [!NOTE]
> The `DELIMITER` command is used in CLI clients to change the standard `;` delimiter so MySQL doesn't prematurely execute lines inside the procedure block.

### Calling Stored Procedures

```sql
-- Call procedure passing inputs and variable targets
CALL GetUserStats(42, @count, @spent);

-- View output values stored in session variables
SELECT @count, @spent;
```

---

## 2. User-Defined Functions (UDF)

Functions return a single scalar value and can be executed inside select statements.

```sql
DELIMITER //

CREATE FUNCTION GetMembershipLevel(p_total_spent DECIMAL(10,2))
RETURNS VARCHAR(20)
DETERMINISTIC
BEGIN
    DECLARE v_level VARCHAR(20);

    IF p_total_spent >= 5000.00 THEN
        SET v_level = 'PLATINUM';
    ELSEIF p_total_spent >= 1000.00 THEN
        SET v_level = 'GOLD';
    ELSE
        SET v_level = 'BRONZE';
    END IF;

    RETURN v_level;
END //

DELIMITER ;
```

### Attributes for Functions

MySQL requires you to specify the deterministic nature of functions to optimize replication:

- **`DETERMINISTIC`**: Always returns the same output for same inputs.
- **`NOT DETERMINISTIC`**: Output can vary (e.g. references `NOW()`).
- **`READS SQL DATA`**: Reads tables but does not write.
- **`MODIFIES SQL DATA`**: Writes to tables.
- **`CONTAINS SQL`**: Does not read or write tables (default).

### Calling Functions

```sql
SELECT id, name, GetMembershipLevel(total_spent) AS tier
FROM customers;
```

---

## 3. Triggers

Triggers are automatically executed in response to table modifications (`INSERT`, `UPDATE`, `DELETE`). They can access previous or incoming values using `OLD` and `NEW` keywords.

```sql
DELIMITER //

CREATE TRIGGER before_order_insert
BEFORE INSERT ON orders
FOR EACH ROW
BEGIN
    -- Prevent order values under $1.00
    IF NEW.amount < 1.00 THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'Minimum order amount is $1.00';
    END IF;

    -- Set audit fields automatically
    SET NEW.created_at = NOW();
END //

DELIMITER ;
```

### OLD & NEW Availability

- **`INSERT`**: Only `NEW` is available.
- **`UPDATE`**: Both `OLD` (original values) and `NEW` (updated values) are available.
- **`DELETE`**: Only `OLD` is available.

---

## 4. Control Flow Syntax

Control flow can only be used inside Stored Procedures, Functions, and Triggers.

### IF Statement

```sql
IF condition THEN
    statements;
ELSEIF other_condition THEN
    statements;
ELSE
    statements;
END IF;
```

### CASE Statement

```sql
CASE variable
    WHEN val1 THEN statements;
    WHEN val2 THEN statements;
    ELSE statements;
END CASE;
```

### Loops (WHILE, LOOP, REPEAT)

#### WHILE

```sql
DECLARE counter INT DEFAULT 0;

WHILE counter < 10 DO
    SET counter = counter + 1;
END WHILE;
```

#### LOOP (with LEAVE/ITERATE)

```sql
my_loop: LOOP
    SET counter = counter + 1;
    IF counter >= 10 THEN
        LEAVE my_loop; -- Analogue to break
    END IF;

    IF counter % 2 = 0 THEN
        ITERATE my_loop; -- Analogue to continue
    END IF;
END LOOP my_loop;
```

---

## 5. Cursors & Error Handling

Cursors are used to loop through query result sets row by row in procedural code.

```sql
DELIMITER //

CREATE PROCEDURE ProcessUnpaidInvoices()
BEGIN
    DECLARE done INT DEFAULT FALSE;
    DECLARE v_id INT;
    DECLARE v_amount DECIMAL(10,2);

    -- 1. Declare cursor
    DECLARE invoice_cursor CURSOR FOR
        SELECT id, amount FROM invoices WHERE status = 'unpaid';

    -- 2. Declare continue handler for cursor end
    DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = TRUE;

    -- 3. Open cursor
    OPEN invoice_cursor;

    read_loop: LOOP
        -- 4. Fetch data into variables
        FETCH invoice_cursor INTO v_id, v_amount;
        IF done THEN
            LEAVE read_loop;
        END IF;

        -- 5. Process record
        UPDATE accounts SET debt = debt + v_amount WHERE user_id = v_id;
    END LOOP;

    -- 6. Close cursor
    CLOSE invoice_cursor;
END //

DELIMITER ;
```

---

## 6. Views

Views are virtual tables defined by a stored query.

### Create View

```sql
CREATE OR REPLACE VIEW active_staff AS
SELECT id, first_name, last_name, email
FROM employees
WHERE role = 'Staff' AND is_active = TRUE;
```

### Updatable Views

Views can support `INSERT`/`UPDATE` operations if they map directly to a single table without groupings, aggregates, joins, or distinct parameters.

```sql
CREATE OR REPLACE VIEW regional_managers AS
SELECT id, name, region
FROM employees
WHERE role = 'Manager'
WITH CHECK OPTION;
-- WITH CHECK OPTION prevents updates that make the row fall outside the view filter.
```

If you run:

```sql
UPDATE regional_managers SET region = 'Europe' WHERE id = 12; -- Works
UPDATE regional_managers SET role = 'Staff' WHERE id = 12; -- Fails CHECK OPTION
```

---

## 7. Error Handling (SIGNAL)

Use `SIGNAL` to raise custom exceptions and abort execution.

```sql
SIGNAL SQLSTATE '45000'
SET MESSAGE_TEXT = 'An error occurred during operation execution.';
```

- **`45000`**: The generic SQLSTATE code indicating user-defined unhandled exception.
