## Usage of ES6 spread operator

Spread operator allows us to spread the content of an array. Let's consider some cases when we can use it:

### Passing array's values as arguments:

	const numbers = [1,2,3];

	const addThreeNumbers = (num1, num2, num3) = > {
		return num1 + num2 + num3 }

Of course, passing an array like this:

	addThreeNumbers(numbers)

would cause error, because it will be interpreted as:

	addThreeNumbers( [1,2,3], undefinied, undefinied)

In that case we should use spread:

	addThreeNumbers(...numbers) // result: 6

It will be interpreted as:

	addThreeNumbers( numbers[0], numbers[1], numbers[2] )

### Getting max or mix value:

We can easily use spread with Math.max and Math.min methods:

	const numbers = [1,2,3];
	Math.max(...numbers) // result: 3
	Math.min(...numbers) // result: 1

It will be interpreted as:

	Math.max( numbers[0], numbers[1], numbers[2] )
	Math.min( numbers[0], numbers[1], numbers[2] )

### Using concat:

While using concat, we can use spread to join arrays' content directly:

	const people = ["John", "Adam", "Bob"]
	const cities = ["London", "Berlin", "Paris"]

	const PeopleAndCities = people.concat(...cities)
	// result: ["John", "Adam", "Bob", "London", "Berlin", "Paris"]

If we didn't use spread, we would get:

	[ "John", "Adam", "Bob", [cities] ]
