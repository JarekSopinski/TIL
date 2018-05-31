Classes were introduced in ES6 and they're basiclly a "sugar syntax" for prototypes.

A new class is declared with a "class" keyword. Its name should start with a capital letter:

	class User

A class needs to have a constructor method, which will create new instances of that class:

	class User {
		constructor() {}
		}

When we want to create new intance of a class, we have to use a "new" keyword. This keyword does three things:

1. It creates a new empty object;
2. It sets the value of "this" to a new empty object;
3. It calls the constructor method.

	const userOne = new User()

To create new class instances with new values we pass them as arguments to a constructor method:

	class User {
		constructor(name, email) {
			this.name = name;
			this.email = email
			}
		}

	const userOne = new User("Jarek", "jarek@gmail.com");
	const userTwo = new User("John", "john@gmail.com");

We can add our own methods to a class:

	class User {

		constructor(name, email) {
			this.name = name;
			this.email = email
			}

		showName() {console.log(this.name)}
		showEmail() {console.log(this.email)}

		}

	const userOne = new User("Jarek", "jarek@gmail.com");
	userOne.showName() // "Jarek"


### Class inheritance

We create a new class which inherits properties from another (parent) class using "extends" keyword:

	class Admin extends User {
		removeUser(){}
	}

Now Admin class will keep all of User class properties. But only instances of Admin class will have acess to removeUser() method. Note that we don't have to declare constructor() method again, because it was already inherited from User.

### Super keyword

The "super" keyword is used to access and call functions on an object's parent. We can use it if we want to add additional properties to a child class. Note that "super" always has to be called before "this" keyword to prevent reference error.

	class Admin extends User {
		constructor (name, email, adminRank){
			super(name,email);
			this.adminRank = adminRank
		}
    	}

	const Adam = new Admin("Adam", "adam@gmail.com", "high")

	Adam.adminRank // "high"
