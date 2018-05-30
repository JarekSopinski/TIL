### Get an object's key using a value

	const getKeyByValue = (object, value) => {
	  return Object.keys(object).find(key => object[key] === value);
	}

	const drinks = {"beer": 5, "vodka": 20}

	getKeyByValue(drinks, 20) // "vodka"
