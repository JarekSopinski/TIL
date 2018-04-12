## How to filter an array through another array?

	let arr1 = ['a', 'b', 'c'];

	let arr2 = ['b', 'c'];

	let arr3 = arr1.filter(letter => !arr2.includes(letter));

	arr3 // returns ['a']
