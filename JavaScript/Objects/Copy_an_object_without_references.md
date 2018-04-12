## How to copy an object without references?

Let's consider following example:

	const polishCity = {name: "Warsaw"};
	const englishCity = polishCity;

	englishCity.name = "London";
	englishCity; // {name: "London"}
	polishCity; // {name: "London"}

As we can see, polishCity was also modified. There are two ways to prevent this:

### Use Object.assign with an empty object:

	const polishCity = {name: "Warsaw"};
	const englishCity = Object.assign({}, polishCity);

	englishCity.name = "London";
	englishCity; // {name: "London"}
	polishCity; // {name: "Warsaw"}

### Use spread operator:

	const polishCity = {name: "Warsaw"};
	const englishCity = {...polishCity};
	
	englishCity.name = "London";
	englishCity; // {name: "London"}
	polishCity; // {name: "Warsaw"}

In both cases, polishCity wasn't modified.
