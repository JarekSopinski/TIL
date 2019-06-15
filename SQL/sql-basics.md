## SQL basics

SQL is a relational database management system.

A database can contain several different tables, which stores a specific type ('model') of data. A tables can be releted by a foreign key (i.e. user ids).

Each table is made of rows and columns. A row represents an individual record in a table. A column represents a property on that record.

A column expects a specific type of data, i.e. an integer or a string.

### Basic queries examples

Note: some examples are php-based.

Select all columns from 'books' table:

    'SELECT * FROM books'

Select specific columns from 'books' table:

    'SELECT title, author, id FROM books'

Select specific columns from 'books' table and order them by date:

    'SELECT title, author, id FROM books ORDER BY date'

Insert new row into 'books' table:

    "INSERT INTO books(title, author) VALUES('$title', '$author')"

Select specific book based on id:

    "SELECT * FROM books WHERE id = $id"