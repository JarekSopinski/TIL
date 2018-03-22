## How to copy (clone) an array without references?

Let's cosider following example:

	const numbersA = [1,2,3];
	const numbersB = numbersA;
	numbersB.push(4);
	numbersB; // [1,2,3,4]
	numbersA; // [1,2,3,4]

numbersA was also modified because of keeping references. 
We can prevent that by using spread operator:


	const numbersA = [1,2,3];
	const numbersB = [...numbersA];
	numbersB.push(4);
	numbersB; // [1,2,3,4]
	numbersA; // [1,2,3]

This time numbersA wasn't modified.
This solution can be repeated in a similar way while coping objects.
