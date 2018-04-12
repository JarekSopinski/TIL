## How to use bind to keep context:

Let's say that we have an object with simple method:

	const person = {
		name: "Jarek",
		sayMyName: function () { return this.name } 
		}

Now we want to pass sayMyName() method to an outside variable:

	const sayMyName = person.sayMyName
	sayMyName() // context (this.name) was lost, will probably refer to window's context

We can fix it using bind:

	const bindSayMyName = sayMyName.bind(person) // we bind context to person's context
	bindSayMyName() // returns "Jarek"

### Another example with bind:

	function Counter() {
		this.counter = 0;
		let increment = function() {
			this.counter++;
			console.log(this.counter);
		};
	
		setInterval(increment.bind(this), 2000);
		// if we typed setInterval(increment, 2000), than increment wouldn't now what is counter
		// context of increment is binded to context of setInterval
	}

If we typed setInterval(increment, 2000), than increment wouldn't know what is counter. Thanks to bind,
its context is now set to the context of setInterval, meaning it 'knows' what's inside Counter function.

An alternative way is to use arrow function. (.this) inside arrow function will refer to upper level. In case arrow function isn't put inside anything else, (.this) will refer to window. In the case below, (.this) refers to parent function - ArrowCounter, meaning that setInterval (our arrow function) will 'know' the value of counter:

	function ArrowCounter() {
		this.counter = 0;
		setInterval(() => {
			this.counter++;
			console.log(this.counter);
		}, 2000);
	}


