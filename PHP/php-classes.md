## PHP Classes

### Introduction

A new class is created using the 'class' keyword and should be named with a capital letter:

    class User {

    }

Classes can have public and private properties. Public properties can be accessed and modified from anywhere within the code. Private properties can be accessed from inside the class itself. Usually properties should be set to private and be modified only by its class functions.

Below is an example of a simple class with two properties and a function:

    class User {

        public $email;
        public $name;

        public function login(){
            echo 'the user logged in';
        }

    }

Now we can create a new object based on the User class using 'new' keyword:

    $userOne = new User();

And now we can invoke the login() function:

    $userOne->login();

We can set properties using the built-in constructor function. Notice the use of $this keyword and how login() function was modified to make use of properties' values:

    class User {

        public $email;
        public $name;

        public function __construct(){
            $this->email = $email;
            $this->name = $name;
        }

        public function login(){
            echo $this->name . ' logged in';
        }

    }

While creating new object we can set its values using previously definied constructor:

    $userOne = new User('john', 'john@gmail.com');

And now we can access these values - notice that we don't use the dollar sign:

    echo $userOne->name;
    echo $userOne->email;
    $userOne->login(); // 'john logged in'