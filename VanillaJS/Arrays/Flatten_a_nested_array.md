## How to flatten a nested array?

Let's say that we have an array with many sublevels and we want to convert it into an array with only one level.	

### Example:

	const arr = [[[[1]]],[2],[[3]],[4]];

	function makeArrayFlat(initialArray) {
		let flatArray = [].concat(...initialArray);
		return flatArray.some(Array.isArray) ?
		    makeArrayFlat(flatArray)
		    :
		    flatArray;
	    }

	makeArrayFlat(arr) // returns [1,2,3,4]

### Explanation:

We use ternary operator to check if there are any sublevels (another arrays). If there are, we use recursion with updated array as an argument. Function calls itself back, until there are no sublevels left.
