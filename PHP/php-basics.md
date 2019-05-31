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