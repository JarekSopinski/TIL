## How to run tests using Yarn

### Example:

	//sum.js

	export default (a, b) => (a + b)

	//sum.test.js

	import sum from './sum'

	test ('Exprect sum to sum numbers', () => {
	    expect(sum(1,2)).toBe(3)
	    expect(sum(0.1, 0.2)).toBe(0.3)
	})

### Commands

// To run test using yarn type 'yarn test' in console

	'yarn test --coverage' returns raports (exported to newly created catalog 'coverage')


