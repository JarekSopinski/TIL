## SQL and PHP

### MySQLi intro

One of the ways to connect PHP and SQL is to use the MySQLi module.

    // connect to database:
    $conn = mysqli_connect($db_host, $db_user, $db_pass,  $db_name);

    // check database connection
    if (!$conn){
        echo 'Connection error: ' . mysqli_connect_error();
    } else {
        echo 'Connection success';
    };

### Use MySQLi to fetch data as an associative array

To get data using MySQLi we can use mysqli_query function, which takes two arguments: connection settings and the sql query:

    $conn = mysqli_connect($db_host, $db_user, $db_pass,  $db_name);
    $sql_query = 'SELECT title, author, id FROM books';

    $result = mysqli_query($conn, $sql_query);

Than we can convert data to an associative array using mysqli_fetch_all() function:

    $books_assoc_arr = mysqli_fetch_all($result, MYSQLI_ASSOC);

After that, it's a good pracitce to free the result from memory and close the connection to database:

    mysqli_free_result($result);
    mysqli_close($conn);

### Use MySQLi to save data to a database

If we want to save a variable(s) to a database, we first have to use mysqli_real_escape_string() function, which escapes special characters in a string for use in an SQL statement. This function takes two arguments: connection settings and a value that we want to save.

    $conn = mysqli_connect($db_host, $db_user, $db_pass,  $db_name);

    $title_esc = mysqli_real_escape_string($conn, $title);
    $author_esc = mysqli_real_escape_string($conn, $author);

Than we write a query and, as in previous example, pass it to mysqli_query() function.

    $sql_query = "INSERT INTO books(title, author) VALUES('$title_esc', '$author_esc')";

    mysqli_query($conn, $sql_query);