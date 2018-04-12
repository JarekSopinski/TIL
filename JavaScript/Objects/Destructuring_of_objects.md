## Destructuring of objects in ES6

Let's consider following example:

	const city = { name: "Gdansk", age: 1000, population: 500000 }

If we want to pass each value to a new variable, the classic way would be:

	const cityName = city.name;
	const cityAge = city.age;
	const cityPopulation = city.population

But if we use destructuring:

	const { name: cityName, age: cityAge, population: cityPopulation } = city

And now our new variables will hold each key's value:
	
	cityName // "Gdansk"
	cityAge // 1000
	cityPopulation // 500000

We can simplify this by giving variables the same names as keys' names:

	const { name, age, population } = city

And now our new variables will hold each key's value:

	name // "Gdansk"
	age // 1000
	population // 500000
