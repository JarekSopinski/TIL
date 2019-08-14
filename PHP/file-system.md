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