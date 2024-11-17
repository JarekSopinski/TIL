## SQL basics

### Data definition operations

- CREATE DATABASE - creating databases
- CREATE TABLE - creating tables

- CREATE DATABASE IF NOT EXISTS - not available in Postgresql

- ALTER DATABASE - updating databases
- ALTER TABLE - updating tables

- DROP DATABASE <db_name> - deleting databases

### Key data types

#### Text data types

- CHAR (X) - text with max length of X bytes, shorter text will be space-padded (replaced with blanks), longer text will cause error.
- VARCHAR (X) - text with max length of X bytes, shorter text will not be changed (as opposing to CHAR).
- TEXT - no user-defined max length (db system limits still apply - 65,535 chars in MySQL)
- LONGTEXT - no user-defined max length, not supported in Postgres, different types with different sizes in MySQL
- ENUM - only values from a predefined set of allowed values are accepted

### Numeric data types

- INT, SMALLINT, ... - integer numbers (between mix and max boundaries) are allowed, better performance
- DECIMAL, NUMERIC - decimal numbers with a fixed precision (exact values - numbers will not be rounded) - better precision, worse performance (should be used for prices, where precision is important)
- FLOAT, REAL - decimal numbers with floating points (approximated values - numbers will be rounded) - less precision, better performance

For values like NUMERIC (required) and FLOAT (optional) we declare precision (max total amount of digits) and scale (max digits after dot). For example, FLOAT(5,2) allows 123.12 (5 digits for precision, 2 for scale) but does not allow 1234.12 (6 digits for precision).

### Date

- DATE - a value like 1986-10-20 (i.e. no hours or minutes)
- DATETIME, TIMESTAMP - a value like 1986-10-20 14:39:05 (i.e. with hours, minutes etc.)

### Other

- BOOLEAN
- JSON

### Creating an enum

- MySQL:
  - table_name ENUM('v1', 'v2', ...)
- Postgresql:
  - CREATE TYPE my_enum AS ENUM ('v1', 'v2', ...)
  - table_name my_enum

### NULL Values, Inserting (No) Data & Default Values

If a column is omitted in INSERT statement, it'll store NULL.

NULL is not the same as 0 or 0.0 for numeric columns or '' (i.e. an empty string, empty text) for text columns.

Columns can have default values, using DEFAULT keyword. If you have a table definition where some (or all) columns have default values, those columns can never store NULL as a value - even if no specific data is provided in the INSERT INTO statement. The reason for that is, that the DEFAULT value will be used if no specific data is provided.

### Constraints

- NOT NULL - this column must contain a (valid) value - omitting is not possible.

    CREATE TABLE users (
        full_name VARCHAR(300) NOT NULL
    );

- UNIQUE - No duplicate values are allowed.

(PRIMARY KEY 'combines' NOT NULL and unique)

- CHECK - Value must pass a specific check.

- FOREIGN KEY - Value must exist in another table.

There are also column constraints, for example:

    salary INT CHECK (salary > 0)

And table constraints, for example:

    salary INT,
    CHECK (salary > 0)

### Setting unique IDs & Primary Keys

    CREATE TABLE users (
        id INT PRIMARY KEY AUTO_INCREMENT,
        full_name VARCHAR(300) NOT NULL
    );

PRIMARY KEY is a constraint - combination of NUT NULL and UNIQUE.
Only one per table is allowed.

AUTO_INCREMENT is an attribute - the db system will automatically insert and incrementing value.
This works only in MySQL, in most environments use:

    SERIAL PRIMARY KEY

instead of

    INT PRIMARY KEY AUTO_INCREMENT

### Text: encoding and collation

- Encoding - which characters are supported and can be stored?
Encodings can be set on database, table or column level.

- Collation - how are characters compared (e.g. for ordering)?
Collation can be set on database, table or column level.

### More ways of creating tables

- Temporary Tables

Tables that are stored only temporarily (in memory of the db server).
Useful for non-permanent data (e.g. intermediate results).

    CREATE TEMPORARY TABLE ...

- Tables based on other tables / data

Creates a table and pre-populates it with data from a query result set.
Useful if a subset of data from another table should be stored in a separate table.

    CREATE TABLE ... AS <query>