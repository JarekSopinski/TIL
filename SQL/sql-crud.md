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

### Updating data

    UPDATE <table name>
    SET <column name> = <new value>, ...
    WHERE <condition>;

### Deleting data

    DELETE FROM <table name>
    WHERE <condition>;