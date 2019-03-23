## PHP Basics

### Variables

Variables are declared with $ sign. A variable name must start with a letter (lowercased or uppercased) or underscore, but now with a number or a special character.

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