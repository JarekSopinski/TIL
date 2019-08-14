## PHP Basics

### Variables

Variables are declared with $ sign. A variable name must start with a letter (lowercased or uppercased) or underscore, but not with a number or a special character.

    $foo;
    $_foo_bar;
    $FooBar;
    $2foo; // error


A 'dollar' variable can be overwritten. But we can also declare a constant variable using define() function. It's a common practice to write constants' names with capital letters. If we want to gain access to this variable later, we don't use a dollar sign.

    define('NAME', 'jarek');
    echo NAME;

### Strings

String can be concatenated using a dot:

    $foo = 'foo';
    $bar = 'bar';
    echo $foo . $bar; // foobar
    echo $foo . 'abc'; // fooabc

We can insert a variable inside a string, but only with double quotation marks:

    $planet = 'Earth';
    echo "I come from planet $planet"; // echoes: I come from planet Earth (double quot. marks)
    echo 'I come from planet $planet'; // echoes: I come from planet $planet (single quot. marks)

To prevent escaping characters, we can use backslash before that character:

    echo "I said "stop!" with a loud voice"; // error
    echo "I said \"stop!\" with a loud voice"; // echoes: I said "stop!" with a loud voice

We can gain access to a sign inside a variable by providing its index (counted from 0):

    $name = 'jarek';
    echo $name[0]; // echoes: j

strlen() function returns the length of a string:

    $name = 'jarek';
    echo strlen($name); // echoes: 5

strtoupper() function returns uppercased string. strtolower() does the opposite.

    $name = 'jarek';
    echo strtoupper($name); // echoes: JAREK

str_replace() function replaces every character in a string with another. It takes 3 arguments: 1) replaced character 2) new character 3) string variable.

    $name = "jarek";
    echo str_replace('j', 'm', $name); // echoes: marek

explode() function can be used to convert a string into an array:

    $drinks = 'tea, cola, beer';
    explode(',', $drinks); // Result: ['tea', 'cola', 'beer'];

### Numbers

Number can be an integer (i.e. 25) or a float (i.e. 3.14). PHP provides basic math operations: +,-,*,/,**. They are performed in BODMAS order (bracets, orders, division, multiplication, adding, subtracting). We can use shorthand operators (+=, -=, *=). We can also increment (++) and decrement (--).

floor() function floors a float to a nearest integer. ceil() takes it up to a nearest integer.

    $pi = 3.14;
    echo floor($pi); // echoes 3
    echo ceil ($pi); // echoes 4

### Functions

PHP functions are declared and called like this:

    function sayHello($name) {
        echo "Good morning $name";
    };

    sayHello('John'); // echoes "Good morning John"

Parameters can have default values. That way our code won't break if we won't provide an argument for this parameter. Of course, if an argument is provided, it will always overwrite the default value.

    function sayHello($name = 'John') {
        echo "Good morning $name";
    };

    sayHello(); // echoes "Good morning John"
    sayHello('Adam'); // echoes "Good morning Adam".

A function can return a value, which can be stored inside a variable:

    function addNums($num1, $num2) {
        return $num1 + $num2;
    };

    $sum = addNums(2,3);

### Scope

A variable can have a local scope or a global scope.

For example, variables declared inside functions have local scope:

    function myFunc() {
        $num = 10; // local scope
        echo $num;
    };

    myFunc(); // echoes 10
    echo $num; // nothing, can't access a variable

Parameters also have local scope:

    function myFunc($num = 10) {
        echo $num;
    };

    myFunc(); // echoes 10
    echo $num; // nothing, can't access a variable

On the other hand, a function can't simply access a global variable:

    $num = 10; // global scope
    function myFunc() {
        echo $num;
    };
    myFunc(); // nothing, can't access a variable

If we want to use a global variable inside a function, we have to inform a function about it, by using a keyword 'global':

    $num = 10; // global scope
    function myFunc() {
        global $num;
        echo $num;
    };
    myFunc(); // echoes 10;

Keep in mind that changing a value of a variable called by a 'global' keyword inside a function will also change the value outside (if the function is executed):

    $name = 'John';
    function changeName() {
        global $name;
        $name = 'Adam';
    };

    echo $name; // 'John' - value wasn't changed because function wasn't executed yet
    changeName();
    echo $name; // 'Adam'; - value was changed after the function was executed

If we want to use a global as a parameter and than update its global value, we have to call it with an ampersand (&):

    $name = 'John';
    function changeName(&$name) {
        $name = 'Adam';
    };
    changeName($name);
    echo $name; // 'Adam' - global value was updated. 
    
If we didn't use an ampersand (&) in the example above, we would still get 'John', because a value would be updated only in a local scope.

Constants always have a global scope, so we don't have to use keyword 'global' to use them.

    define ('NAME', 'Jarek');
    function myFunc() {
        echo NAME; // no need for 'global'
    };
    myFunc(); // echoes 'Jarek';

### Include and require

Include and require are PHP built-in functions which allow us to include a file into another file. The main difference is the fact, that require will cause fatal error if the file wasn't found, while include won't stop the rest of the code from being executed.

    include('file.php');
    require('file.php');

Brackets can be omitted:

    include 'file.php';
    require 'file.php';

### Ternary operators

PHP supports ternary operators, which are written like this:

    $val = 50;
    $res = $val > 40 ? 'high' : 'low';
    echo $res;

### Superglobals

Superglobals are special variables, containing arrays with various data, which are available in PHP. They include:

* $_GET - data from GET request
* $_POST - data from POST request
* $_SERVER - data about server, i.e. 
    ⋅⋅* $_SERVER['name']
    ⋅⋅* $_SERVER['REQUEST_METHOD'] 
    ⋅⋅* $_SERVER['SCRIPT_FILENAME']
    ⋅⋅* $_SERVER['PHP_SELF']
    ..* $_SERVER['QUERY_STRING']
* $_SESSION - explained below
* $_COOKIE - explained below

### Sessions

A session superglobal ($_SESSION) can keep data on a server, between various pages (reloads), as long as a session lasts (until user leaves).
Before it can be used, we have to call session_start() function.

For example, if a user submited some data by a POST request, we can keep these data:

    if ( isset($_POST['submit']) ) {
        session_start();
        $_SESSION['name'] = $_POST['name'];
        header('Location: index.php');
    }

In this case, 'name' value will be still availabe after redirection to index.php.

We can unset a single session variable or all of them:

    unset($_SESSION['name']); // removes 'name'
    session_unset; // removes all session variables

### Null coalescing

Using a double quotation mark, we can easily set a default backup value, that will prevent null errors:

    $name = $_SESSION['name'] ?? 'Guest';
    echo $name; // now in case of empty $_SESSION['name'] we won't get an error, but a backup value ('Guest') instead

### Cookies

While sessions are stored on a server, cookies are stored on a user's computer. A cookie is created using setcookie() function, which takes three arguments: cookie name, data and expire time.

    setcookie('user_name', $_POST['user_name'], time() + 86400);

Than we can get cookie's value using $_COOKIE superglobal with correct cookie name:

    $user_name = $_COOKIE['user_name'] ?? 'Unknown';