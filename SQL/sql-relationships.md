## SQL Data normalization and relationships

Data can be connected across multiple tables using foreign keys. The primary key of table A can be used as a foreign key of table B. Every table has at most one primary key, but every table may use multiple foreign keys.

### Data normalization

A concept that reduces data redundancy and increases data maintainability by splitting data across multiple tables. We should aim for having one table per one entity.

Multiple rows with duplicate data (in a single table) should be avoided. Having duplicate data in some columns in multiple rows is a clear indicator of suboptimal data normalization.

### Six forms of normalization

1) First Normal Form: Each table cell (column + row) should contain a single value. Each row (record) should be unique.
2) Second Normal Form: There are no duplicate row values because of multi-column keys (composite keys).
3) Third Normal Form: All column values in a row are dependant on only the primary key.
4) Boyce-Codd Normal Form: There must be no conflicting unique identification criteria -> Avoid multiple entities in one table.
5) Fourth Normal Form: All combinations of all (non-key) cell values should be possible.
6) Fifth Normal Form: There are no clashing column values because of implicit column dependencies.

#### Simple rule:

Avoid mixing data entities in the same table, avoid multiple values in a single table cell but also try to avoid splitting basic data across dozens of tables!

### INNER JOIN

A command used to select data across multiple tables. In the example below we join addresses and users table, when users have foreign key to addresses.

Aliases (AS) are not required, but they're often used to shorten such statements. We can also use aliases for column names for the result set.

Dot notation is required for columns that can have the same sames in joined tables, to be precise to which column we refer.

The result only includes rows that have matching data, where connection could be established.

    SELECT u.first_name, a.street, a.house_number
    FROM users AS u
    INNER JOIN addresses AS a ON u.address_id = a.id

We can also combine multiple JOIN statements, for example:

    SELECT u.id, first_name, last_name, street, house_number, c.name AS city_name
    FROM users AS u
    INNER JOIN addresses AS a ON u.address_id = a.id
    INNER JOIN cities AS c ON a.city_id = c.id;

JOIN statements can be followed by filtering and sorting statements, for example:

    SELECT u.id, first_name, last_name, street, house_number, c.name AS city_name
    FROM users AS u
    INNER JOIN addresses AS a ON u.address_id = a.id
    INNER JOIN cities AS c ON a.city_id = c.id
    WHERE c.id = 1 OR c.id = 2
    ORDER BY u.id DESC;

### LEFT JOIN

As was mentioned, INNER JOIN only includes rows that have matching data.
Meanwhile, LEFT JOIN includes ALL rows from 'left' table (in the example, 'addresses') and only matching rows from 'right' table (in the example 'users'). So if we have a user that doesn't have address_id, it wouldn't be included.

'Left' table is the 'base table' defined in the FROM statement. 'Right' table is a table that is being joined.

    SELECT u.first_name, a.street, a.house_number
    FROM addresses AS a
    LEFT JOIN users AS u ON u.address_id = a.id

Lets say we combine multiple LEFT JOIN like this:

    SELECT *
    FROM addresses AS a
    LEFT JOIN users AS u ON a.id = u.address_id
    LEFT JOIN cities AS c ON c.id = a.city_id

In this example, on the 4th line, a 'left table' is not just 'users', but the result of all previous joins (addresses and users)!

There is also RIGHT JOIN statement, but by a conventions, it's not often used, as it would give the same results as LEFT JOIN, only with different order.

### CROSS JOIN

Joins all entries from given tables, resulting in all possible combinations, even if there's no relation. Doesn't require 'AS' statement. Rarely used.

### UNION

Difference:
JOIN is a clause that MERGE multiple tables into one result set by appending COLUMNS.
UNION is a clause that COMBINES multiple result sets into one result set by appending ROWS.

UNION is helpful if we have rows returned by one select statement and we want to merge them with rows returned by another select statement.

    SELECT * FROM users WHERE id < 3
    UNION
    SELECT * FROM users WHERE id = 3

Note that UNION cannot be used if result sets have different number of columns.

### Data relationship types

- One-to-Many (1:n) - one record in table A has one or many related records in table B, but every record in table B can have only one reference to table A.
- Many-to-Many (n:n) - one record in table A has one or many related tables in table B - and vice versa
- One-to-One (1:1) - one record in table A belongs to exactly one record in table B - and vice versa

#### Intermediate tables

In case Many-to-Many relationships, we require 'intermediate tables'. For example, if we want to create n:n relationship between 'employees' and 'projects' tables (one employee can have multiple projects, one project can have multiple employees), we create 'projects_employees' intermediate table, that has 'project_id' and 'employee_id' foreign keys.

    CREATE TABLE projects_employees (
        id SERIAL PRIMARY KEY,
        employee_id INT REFERENCES employees ON DELETE CASCADE,
        project_id INT REFERENCES projects ON DELETE CASCADE
    );

### Self-Referential Relationships

A data entity has a relationship to itself (i.e. internal relationship).

    CREATE TABLE employees (
        id SERIAL PRIMARY KEY,
        first_name VARCHAR(300) NOT NULL,
        last_name VARCHAR(300) NOT NULL,
        supervisor_id INT REFERENCES employees ON DELETE SET NULL -- self-reference
    );

In this example, we can run query to get list of employees with their supervisors.
The query joins results from the same table! We're joining the table with itself, but using different aliases.

    SELECT *
    FROM employees AS e1
    INNER JOIN employees AS e2 ON e1.supervisor_id = e2.id;