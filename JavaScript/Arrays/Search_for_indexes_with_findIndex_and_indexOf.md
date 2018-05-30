## Search for indexes in an array

### Array.prototype.indexOf()

This method returns the first index at which an element can be found. If element wasn't found, it returns -1.

It accepts two arguments: an element that we're looking for and (optionally) an index from which the search should start.

	const colors = ["red", "blue", "green", "yellow", "red"];
	colors.indexOf("red"); // returns 0
	colors.indexOf("red", 1); // returns 4
	colors.indexOf("oragne"); // returns -1

### Array.prototype.findIndex()

This method returns the first index of an element that passed given test. Otherwise it returns -1.

The main difference between findIndex() and indexOf() is the fact that findIndex() accepts a callback as an argument. For example:

	const numbers = [1,5,12,50];
	numbers.findIndex(number => number > 10); // returns 2 - index of 12
	numbers.findIndex(number => number > 100); // returns -1 - no number is bigger than 100



