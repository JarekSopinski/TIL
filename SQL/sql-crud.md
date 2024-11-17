## SQL CRUD OPERATIONS

Create data -> INSERT INTO
Read data -> SELECT
Update data -> UPDATE
Delete data -> DELETE

### Inserting data

Basic command structure:

    INSERT INTO <table name> (<column names>)
    VALUES (<column values>);

Column names can be omitted if we insert data in the same order.

Multiple rows can be added in a single command:

    INSERT INTO <table name> (<column names>)
    VALUES
        (<column values>), -- row 1
        (<column values>), -- row 2
        (<column values>); -- row 3

Insert values returned by a query:

    INSERT INTO <table name> (<column names>)
    SELECT <query>;

### Selecting data

Basic command structure (fetch all data):

    SELECT <columns> FROM <table name>;

Fetch data based on condition:

    SELECT <columns> FROM <table name> WHERE <condition>;

Order results:

    SELECT * FROM <table> ORDER BY <column name>; // ASC by default

    SELECT * FROM <table> ORDER BY <column name> DESC;

Limit results:

    SELECT * FROM <table> LIMIT <number X>;

    SELECT * FROM <table> LIMIT <number X> OFFSET <offset number Y>; // select X number after skipping Y number

Distinct - drop any duplicates in the result set:

    SELECT DISTINCT * FROM <table>;

Filtering syntax:

- Equality: = (for most cases), IS (for comparison to null)
- Inequality: <>, != (alias for <>), IS NOT (for comparison to null)
- Greater or equal: >, >=, BETWEEN
- Smaller or equal: <, <=, BETWEEN
- Combine multiple conditions: AND, OR

### Updating data

    UPDATE <table name>
    SET <column name> = <new value>, ...
    WHERE <condition>;

### Deleting data

    DELETE FROM <table name>
    WHERE <condition>;

### Subqueries

Queries can be 'connected'. We can run a query on a result of another query. This another query has to be in brackets at it needs to have an alias.

    SELECT customer_name, product_name
    FROM (SELECT * FROM sales
    WHERE volume > 1000) AS base_result; // required alias

We can also 'store' a query using a VIEW command. Such stored query can than be used in other queries. In the example below, query is stored as 'base_result'. Notice that query (NOT its result!) is actually stored in db engine itself and will not be lost.

    CREATE VIEW base_result AS
    SELECT * FROM sales
    WHERE volume > 1000

    SELECT customer_name, product_name
    FROM base_result;