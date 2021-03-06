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

array_pop() removes the last element from an array. The removed value can be stored inside a variable.

    $colors = ['red', 'blue', 'green'];
    $removed = array_pop($colors); // $colors = ['red', 'blue']; $removed = 'green'

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

If we are calling an index from associative array and we want to put it inside a string, we have to use curly braces:

    echo {"Product name is $product['name']"};

### Multidimensional array

PHP arrays can be nested within other arrays like this:

    $books = [
        ['lord of the rings', 'jrr tolkien', 3000],
        ['game of thrones', 'grr martin', 1000],
        ['eye of the world', 'robert jordan', 800]
    ];

    $books[1]; // ['game of thrones', 'grr martin', 1000]
    $books[1][0]; // 'game of thrones'

Multidimensional arrays can also be associative:

    $books = [
        ['title' => 'lord of the rings', 'author' => 'jrr tolkien', length => 3000],
        ['title' => 'game of thrones', 'author' => 'grr martin', length => 1000],
        ['title' => 'eye of the world', 'author' => 'robert jordan', length => 800]
    ];

    $books[1]['title']; // 'game of thrones'

New nested array can be inserted like this:

    $books[] = ['title' => 'name of the wind', 'author' => 'patrick rothfuss', length => 600];

### Loops

Here's and example of a for loop, with number of iterations equal to a length of an array:

    $blogs = ['blog1', 'blog2', 'blog3', 'blog4', ...];

    for ($i = 0; $i<count($blogs); $i++) {
        echo $blog[$i];
    };

Here's an example of a foreach loop:

    foreach ($blogs as $blog) {
        echo $blog;
    };

And an example of a while loop:

    $i = 0;
    while ($i < count($blogs)) {
        echo $blogs[$i];
        $i++;
    }

A loop can be exited using 'break' keyword.

    $nums = [1,2,3,4];
    foreach ($nums as $num) {
        if ($num > 2) {
            echo 'stop';
            break; // loop will skip indexes 3 and 4
        }
    }

A 'continue' keyword stops the execution of the rest of the code inside the loop (below that keyword), but doesn't stop the loop itself.

    $nums = [1,2,3,4];
    foreach ($nums as $num) {
        if ($num > 2) {
            echo 'foo';
            continue;
            echo 'bar'; // this line won't execute on indexes 3 and 4
        }
    }