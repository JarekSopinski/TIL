## SQL and PHP

### MySQLi

One of the ways to connect PHP and SQL is to use the MySQLi module.

    // connect to database:
    $conn = mysqli_connect($db_host, $db_user, $db_pass,  $db_name);

    // check database connection
    if (!$conn){
        echo 'Connection error: ' . mysqli_connect_error();
    } else {
        echo 'Connection success';
    };