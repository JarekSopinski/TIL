## How to put all arguments into single array?

First solution: [Array.from() (ES6)](https://developer.mozilla.org/pl/docs/Web/JavaScript/Referencje/Obiekty/Array/from)

	foo (arg1, arg2, arg3) => {	
		const argsArray = Array.from(arguments);
		return argsArray
		} // returns [arg1, arg2, arg3]
		
		
Second solution: [Spread operator](https://github.com/JarekSopinski/TIL/blob/master/JavaScript/Arrays/Spread_operator.md)

	bar (arg1, arg2, arg3) => {	
		const argsArray = [...arguments];
		return argsArray
		} // returns [arg1, arg2, arg3]
		
Reference: [Arguments object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments)
Please note: Arguments object is not available in arrow functions!
