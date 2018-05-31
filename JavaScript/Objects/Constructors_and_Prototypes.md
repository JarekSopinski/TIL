## Constructors and prototypes

Before ES6 classes were introduced, new instances of an object were builded using constructor functions. Constructors are still the foundation for classes and they are used "under the hood".

Here is an example of creating a constructor and a new instance of this constructor:

	function User(name, email){
		this.name = name;
		this.email = email
		this.showName = function(){console.log(this.name)}
	}

	const jarek = new User("jarek", "jarek@gmail.com")

	jarek.showName() // "jarek"

Every object inherits its properties and methods from a prototype. Newly created "jarek" object inherits from "User" prototype, and "User" inherits from Object prototype, which is on the top of the whole inheritance chain.

A constructor function has a prototype property, that we can call:

	User.prototype

This way we can add new methods to a constructor:

	User.prototype.showEmail = function(){console.log(this.email)}
	jarek.showEmail() // "jarek@gmail.com"

## Prototype inheritance

Now let's create an "Admin" constructor that will inherit from the "User" constructor. One of the way to achive this is by using apply() method and rest parameter (...):

	function Admin(...arguments){
		User.apply(this, arguments)
	}

If we didn't use rest, our constructor would look like this:

	function Admin(name, email){
		User.apply(this, [name, email])
	}


Let's say that we want "Admin" to have a "rank" property that only admins - not users - should have:

	function Admin(rank, ...arguments){
		User.apply(this, arguments);
		this.rank = "high"
	}

	const joe = new Admin("high","joe","joe@gmail.com")

Now, remember that we declared showEmail() method using prototype property. While every instance of "User" will have access to that method, instances of "Admin" won't have access:

	jarek.showEmail() // "jarek@gmail.com"
	joe.showEmail() // error, showEmail in not a function

We can use Object.create() method so that Admin's prototype will point to User's prototype:

	Admin.prototype = Object.create(User.prototype)

Now, every new instance of "Admin" will have access to showEmail() method. Note that this relates only to instances created after using Object.create.

	joe.showEmail() // error, showEmail in not a function
	const bob = new Admin("medium","bob","bob@gmail.com")
	bob.showEmail() // "bob@gmail.com"

Of course, we can add a new method only for "Admin". Instance of "User" won't have access to that method:

	Admin.prototype.removeUser = function(){...}


