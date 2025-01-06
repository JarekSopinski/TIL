## SQL Transactions

SQL statements can be bundles into a transaction block.

We can then decide what to do, when all statements run in a transaction block: undo changes or commit changes.

Until transaction ends, data are stored only in memory of a session and not actually saved to database.

    -- START TRANSACTION; -- MySQL
    BEGIN; -- Postgresql

    -- Notice that starting new transactions always commits previous transaction.

    -- First statement
    INSERT INTO customers(first_name, last_name, email)
    VALUES(
            'Maria',
            'Jones',
            'maria@test.com'
    );

    -- Save point between statements
    SAVEPOINT save_1;

    -- Second statement
    INSERT INTO orders(amount_billed, customer_id)
    VALUES(103.12, 4);

    -- ROLLBACK; -- rollback ALL transaction changes (MySQL and Postgresql)
    -- ROLLBACK TO save_1; -- rollback to a specific save point
    COMMIT; -- rollback transaction changes (MySQL and Postgresql)