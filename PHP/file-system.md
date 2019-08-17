## PHP file system

The most basic way to read files in PHP is to use readfile() function:

    $file = 'quotes.txt';

    if (file_exists($file)) {
        $quotes = readfile($file);
        echo $quotes; // echoes file's content and it's size
    }

There are a few other functions, that can be used to perform basic files and directories manipulation or retrieve files' data:

    copy ($file, 'quotes-copy.txt'); // creates a copy of a file
    rename($file, 'changed.txt'); // changes name
    mkdir('files'); // created directory

Functions that retrieve files' data include:

    realpath($file); // returns an absolute path to the file
    filesize($file); // returns a size of the file in bytes

fopen() function can be used to store reference to a file for further operations:

    $handle = fopen($file, 'r'); // second argument means read only

    echo fread($handle, filesize($file)); // read the whole file
    echo fread($handle, 112); // read first 112 bytes
    echo fgets($handle); // get (read) single line
    echo fgetc($handle); // get (read) single character

    $handle = fopen($file, 'r+'); // change second argument to allow writing

    $write($handle, "\nNew text to write"); // write a new line

It should be noticed that reading and writing functions reference text by a pointer. If, in example, we want to write from the end of the file, we have to use 'a+' argument, which will place a pointer at the end:

    $handle = fopen($file, 'a+');
    $write($handle, "\nNew text at the end");

It's always good to close a file, after we're finished:

    fclose($handle);

A file can also be deleted using the unlink() function:

    unlink($file);