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

### Getters and setters

As mentioned above, class properties shouldn't be directly accessed and modified from outside of the class itself. Instead, they should be set to private. If we want to access or modify a private property, we have to use a getter or a setter function.

    class User {

        // private properties can't be accessed directly:
        private $email;
        private $name;

        public function __construct(){
            $this->email = $email;
            $this->name = $name;
        }

        public function login(){
            echo $this->name . ' logged in';
        }

        // a public getter function to get a name's value:
        public function getName(){
            return $this->name;
        }

        // a public setter function to set a name's value with a little validation:
        public function setName($name){
            if (is_string($name) && strlen($name > 1)){
                $this->name = $name;
                return "name has been updated to $name";
            } else {
                return "not a valid name";
            }
        }

    }

    $userOne = new User('john', 'john@gmail.com');
    echo $userOne->setName(50); // "not a valid name";
    echo $userOne->setName('jarek'); // "name has been updated to jarek"
    echo $userOne->getName(); // "jarek"