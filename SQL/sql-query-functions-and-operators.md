## SQL - Query functions and operators

### Mathematical functions and operators

Examples of mathematical functions:

    SELECT SUM(price * billing_frequency) AS annual_revenue
    FROM memberships;

    SELECT CEIL(consumption)
    FROM memberships;

    SELECT ROUND(consumption, 2)
    FROM memberships;

    SELECT TRUNC(consumption, 2) -- TRUNCATE in MySQL
    FROM memberships;

### String functions

Examples of string functions:

    SELECT CONCAT(first_name, ' ', last_name)
    FROM memberships;

    -- Result will be the same as above with CONCAT - works only in postgresql
    SELECT first_name || ' ' || last_name
    FROM memberships;

    SELECT * FROM memberships
    WHERE LENGTH(last_name) < 4;

### Date functions

    -- Extract months from dates
    SELECT EXTRACT(MONTH FROM last_check_in)
    FROM memberships;

    -- Extract DayOfWeek (DOW) from dates
    SELECT EXTRACT(DOW FROM last_checkin) -- postgresql only (starts from 1)
    -- SELECT WEEKDAY(last_checkin) -- mysql only (starts from 0)
    FROM memberships;

### Date and timestamps intervals

    -- Postgresql only - returns intervals between TIMESTAMPS
    SELECT last_checkout - last_checkin
    FROM memberships;

    -- MySQL only - returns intervals between TIMESTAMPS
    SELECT TIMESTAMPDIFF(MINUTE, last_checkin, last_checkout)
    FROM memberships;

    -- Postgresql only - returns interval between DATES as number of days
    SELECT membership_end - membership_start
    FROM memberships;

    -- MySQL only - returns interval between DATES as number of days
    SELECT DATEDIFF(membership_end, membership_start)
    FROM memberships;

    -- Postgresql only - interval from now to date
    SELECT NOW() - membership_start
    FROM memberships;

    -- MySQL only
    SELECT DATEDIFF(NOW(), membership_start)
    FROM memberships;

### LIKE and pattern matching

LIKE looks for matching pattern. If stated like this, it will return boolean.

    SELECT first_name LIKE '_a%', first_name
    FROM memberships;

'_' - a specific number of characters, but any character
'%' - unknown number of unknown characters

LIKE can also be used for filtering with WHERE:

    SELECT first_name
    FROM memberships
    WHERE first_name LIKE 'J___';

### EXISTS

    -- Return result of query as a boolean
    SELECT EXISTS(
        SELECT first_name, last_name
        FROM customers
        WHERE email = 'john@test.com'
    );

    SELECT o.id FROM orders AS o
    WHERE EXISTS(
        SELECT email
        FROM customers as c
        WHERE o.customer_id = c.id AND c.email = 'john@test.com'
    );

### IN

    -- Get customers with first name John or Adam
    SELECT id
    FROM customers
    WHERE first_name IN('John', 'Adam');

    -- Filter values returned by subquery
    SELECT email
    FROM customers
    WHERE id IN(
        SELECT customer_id
        FROM orders
    );

### Conditional expressions

    SELECT amount_billed,
        CASE WHEN amount_billed > 30 THEN 'Good Day'
            WHEN amount_billed > 15 AND amount_billed <= 30 THEN 'Normal Day'
            ELSE 'Bad Day'
        END
    FROM orders;