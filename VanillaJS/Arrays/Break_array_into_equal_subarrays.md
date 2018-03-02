# How to break an array into subarrays with equal length?

	function breakArray(array, chunkLenght) {
	
		const firstLevelArray = [];

		for (let i = 0; i < array.length; i+=chunkLenght) {
			const subArray = array.slice(i, i + chunkLenght);
			// do something with subArrays
			firstLevelArray.push(subArray)
		}

		return firstLevelArray
	
	}
	

breakArray([1,2,3,4,5,6], 2) // [ [1,2], [3,4], [5,6] ]
