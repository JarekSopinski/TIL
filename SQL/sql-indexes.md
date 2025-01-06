# SQL Indexes

## Why use indexes?

Indexes can enhance performance, especially in case of WHERE statements.

If a column has an index, then WHERE statement does not have to scan entire rows. Index in a way creates a 'separate table' with that single column.

Values are stored in a sorted extra table. A connection to the original row is stored along with index value.

## Problem with too many indexes

Indexes should be used only when needed. Using too many indexes can actually worsen performance.

Whenever index values change, the values have to be updated in both the original table AND the index table.

Therefore indexes should be created for columns often used in WHERE statements, but which are not often updated.

## Different kinds of indexes

- Standard single-column index
- Unique index - values cannot be inserted more than once
- Multi-column index (composite index) - groups multiple columns together
- Partial index - only index specific rows in a column (not available in MySQL)

## EXPLAIN

Query's performance can be analyzed by adding EXPLAIN or EXPLAIN ANALYZE keyword before it.

    EXPLAIN ANALYZE
    SELECT * FROM users
    WHERE salary > 12000;

## Creating indexes

    CREATE INDEX <index name> ON <table name> (<columns names>)

If we want to create an composite index, we can provide multiple columns names.

For example:

    CREATE INDEX salary_idx ON users (salary)

We can also create index forcing unique values:

    CREATE UNIQUE INDEX salary_idx ON users (salary)

In some engines we can create a partial index, that will include only rows meeting a specified condition:

    CREATE INDEX big_salary_idx ON users (salary)
    WHERE salary > 12000;

By default, index might not be used - it depends on how many rows and columns we have. Db engine in analyze phase might decide, that using certain index might not actually be the best option.

Index can be removed with "DROP INDEX [index name]" command.

We can also declare an index when creating a table:

    CREATE TABLE <table_name> (
        ... column definitions,
        INDEX <index_name> (<column>)
    );

Notice that indexes are automatically created when using UNIQUE constraint.