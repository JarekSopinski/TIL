## How to flatten a nested array?

Let's say that we have an array with many sublevels and we want to convert it into an array with only one sub level.	

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
