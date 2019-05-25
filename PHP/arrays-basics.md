## PHP arrays basics

PHP has 3 types of arrays: indexed arrays, associative arrays and multidimensional arrays.

### Indexed arrays

Indexed arrays can be created in two ways:

    $colors = ['red', 'blue', 'green'];
    $animals = array('lion', 'dog', 'cat');
    echo $colors[0]; // echoes 'red'
    echo $animals[1]; // echoes 'dog'

If we want to see array's content in development, we can output it using print_r() (print readable) function:

    print_r($colors); // output: Array ( [0] => red [1] => blue [2] => green )

array_push() function pushes a value at the end of an array:

    array_push($colors, 'yellow'); // $colors = ['red', 'blue', 'green', 'yellow'];

count() function return a length of an array:

    $colors = ['red', 'blue', 'green'];
    count($colors); // 3

array_merge() merges arrays in an order of arguments:

    $colors = ['red', 'blue', 'green'];
    $animals = array('lion', 'dog', 'cat');
    $colors_and_animals = array_merge($colors, $animals);

### Associative arrays

Associative arrays are key-value pairs. We can gain access to each value by providing its key.

    $jedi = ['yoda' => 'master', 'kenobi' => 'knight', 'anakin' => 'padawan'];
    echo $jedi['yoda']; // echoes 'master';

We can add new value like this:

    $jedi['mace windu'] = 'master'; // $jedi = ['yoda' => 'master', 'kenobi' => 'knight', 'anakin' => 'padawan', 'mace windu' => 'master'];

A value can be overwritten:

    $jedi['kenobi'] = 'master';

Associative arrays can be counted and merged the same as indexed arrays:

    count($jedi); // 4