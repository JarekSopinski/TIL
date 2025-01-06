## SQL Functions

Aggregate functions compute a single result from a set of input values.

### Examples of aggregate functions

    SELECT COUNT(DISTINCT booking_date) FROM bookings;

    SELECT
    MAX(amount_billed) AS max_billed,
    MAX(amount_tipped) AS max_tipped
    FROM bookings;

    SELECT SUM(amount_billed) FROM bookings;

    SELECT ROUND(AVG(num_guests), 2) FROM bookings;

### Example of aggregate functions with JOIN and WHERE

    SELECT MAX(num_guests), MAX(num_seats) FROM bookings AS b
    INNER JOIN tables AS t ON b.table_id = t.id
    INNER JOIN payment_methods AS pm ON b.payment_id = pm.id
    WHERE t.num_seats < 5 AND pm.name = 'Cash';

### GROUP BY

GROUP BY can be used to combine result of 'normal' SELECT and an aggregate function.
In the example below, SUM will return 'nr_guests' by each 'booking_date'.

    SELECT booking_date AS date,
    SUM(nr_guests) as nr_guests,
    FROM bookings
    GROUP BY booking_date;

### HAVING

HAVING is a filter (like WHERE) which can be used with GROUP BY. It checks the result of aggregate function. In the query, it is placed after GROUP BY statement.

While WHERE applies to raw data, HAVING applies to aggregated data (by GROUP BY).

    SELECT booking_date, COUNT(booking_date)
    FROM bookings
    GROUP BY booking_date
    HAVING SUM(amount_billed) > 30;

### Nested subqueries

A nested query is a query built on a result of another query.

    SELECT MIN(daily_sum) -- 'Parent query'
    FROM (
        -- Subquery
        SELECT booking_date, SUM(amount_billed) AS daily_sum
        FROM bookings
        GROUP BY booking_date
    ) AS daily_table; -- Result of subquery MUST have an alias!