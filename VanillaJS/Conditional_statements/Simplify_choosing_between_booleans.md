## Simplify choosing of action depending on boolean value:

Let's say that we have some variable and we want to run some action depending on the fact that it's thrutful. The standard way would be of course:

	let bool = "I am thrutful!";
	bool ? console.log( bool ) : console.log( "I am falsy!") // result: "I am thrutful!"

Now, if we change bool to falsy value:

	bool = null;
	bool ? console.log( bool ) : console.log( "I am falsy!") // result: "I am falsy!"
	

But there is a way to simplify it:

	let bool = "I am thrutful!";
	console.log( bool ||  "I am falsy!" ) // result: "I am thrutful!"

	bool = null;
	console.log( bool ||  "I am falsy!" ) // result: "I am falsy!"

