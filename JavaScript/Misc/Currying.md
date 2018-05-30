### Currying

Currying in a technique of translating the evaluation of a function that takes multiple arguments (or a tuple of arguments) into evaluating a sequence of functions, each with a single argument. [(Wikipedia)](https://en.wikipedia.org/wiki/Currying)

For example, using traditional JS function syntax:

	function add (a, b) {
	  return a + b;
	}

	add(1, 2); // returns 3

Above we have a simple function that takes two arguments and return their sum.  Now, let's curry this function:

	function add (a) {
		return function (b) {
			return a + b
			}
		}

Now we can call it like that:

	add(1)(2); // returns 3

And we can rewrite it using arrow syntax:

	const add = (a) => (b) => a + b

Curried function can be used as a callback, for example:

	[1,2,3,4,5].map(add(2)) // returns [3, 4, 5, 6, 7] (each item from an array is passed as b)
	

In React, currying is often used in case of working with Redux Middleware.


