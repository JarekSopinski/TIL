## SQL keys

### Setting unique IDs & Primary Keys

    CREATE TABLE users (
        id SERIAL PRIMARY KEY, -- NOT MySQL
        id INT PRIMARY KEY AUTO_INCREMENT, -- MySQL only
        full_name VARCHAR(300) NOT NULL
    );

PRIMARY KEY is a constraint - combination of NUT NULL and UNIQUE.
Only one per table is allowed.

AUTO_INCREMENT is an attribute - the db system will automatically insert and incrementing value.
This works only in MySQL, in most environments use:

    SERIAL PRIMARY KEY

instead of

    INT PRIMARY KEY AUTO_INCREMENT

Remember: primary keys don't always have to be auto-incrementing integers! Any column can be used as a primary key. There are to requirements: it can't be null (NOT NULL) and must be unique (UNIQUE).

### FOREIGN KEY CONSTRAINTS

We can create references for foreign keys:

    address_id INT REFERENCES addresses (id) ON DELETE CASCADE

Column name ('addresses (id)') can be omitted if we're adding relation to a primary key of given table.

In MySQL a different syntax must be used (above syntax will be ignored):

    some_column_name VARCHAR(200),
      FOREIGN KEY (some_column_name) REFERENCES other_table (other_column) ON DELETE ...

Commands 'ON DELETE' and 'ON UPDATE' define what should happen to a row if a related row is deleted or updated.
In the example above, 'CASCADE' would delete related row.

Possible 'ON DELETE' and 'ON UPDATE' values:
- RESTRICT - prevent the intended action (e.g. deleting a related row)
- NO ACTION (default) - prevent the intended action (e.g. deleting a related row); check can be deferred, e.g. as part of a transaction
- CASCADE - perform the same action (e.g. deleting a related row) on the row with the foreign key
- SET NULL - set the foreign key value to NULL if the related row was deleted
- SET DEFAULT - set the foreign key value to its DEFAULT value if the related row was deleted

### COMPOSITE PRIMARY KEYS

You must only have one primary key per table. BUT! Primary keys can 'span multiple columns', as 'composite keys' (created from multiple columns).

For example, in 'intermediate table' that serves as a relation between tables with n:n relationship, we will have id acting like a 'surrogate key'. It acts as a unique primary identifier, but it isn't the real identification criteria.

    CREATE TABLE projects_employees (
        id SERIAL PRIMARY KEY, -- 'surrogate key'
        employee_id INT REFERENCES employees ON DELETE CASCADE,
        project_id INT REFERENCES projects ON DELETE CASCADE
    );

Therefore we can get rid of this 'surrogate key' and create 'composite primary key' from the combination of employee_id and project_id.

    CREATE TABLE projects_employees (
        employee_id INT REFERENCES employees ON DELETE CASCADE,
        project_id INT REFERENCES projects ON DELETE CASCADE,
        PRIMARY KEY (employee_id, project_id) -- Composite Primary Key
    );

That is not required, of course. There is nothing wrong with using surrogate keys! Surrogate keys actually might be better for performance reasons.

In a similar way to composite primary keys, we can create composite foreign keys.