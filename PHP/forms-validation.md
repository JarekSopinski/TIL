## PHP forms validation

Data from GET and POST requests are stored within $_GET and $_POST variables (as associative arrays):

    if (isset($_POST['submit'])) {
        $email = $_POST['email'];
    };

Data from GET request will be visible in an url.
Data from POST request will NOT be visible in an url.

It's a good pracitce to use htmlspecialchars() function to convert echoed data from requests - this is a basic protection from xss attacks, which can i.e. inject some JavaScript code. htmlspecialchars() converts predefined characters to HTML entities, so if such code would be echoed, it won't execute.

    if (isset($_POST['submit'])) {
        $email = htmlspecialchars($_POST['email']);
    };

PHP has some built-in filters that we can use to perform basic form validation. filter_var() function will filter a variable (first argument) using a specified filter (second argument). For example, FILTER_VALIDATE_EMAIL returns true if valid email is provided:

    filter_var($email, FILTER_VALIDATE_EMAIL);

More advanced validation can be performed using preg_match() function, which performs a regular expression match, i.e.:

    $title = $_POST['title'];
    if(!preg_match('/^[a-zA-Z\s]+$/', $title)) {
        echo 'Title must be letters and spaces only <br />';
    };