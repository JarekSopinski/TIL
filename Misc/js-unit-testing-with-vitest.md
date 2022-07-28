## Unit Testing in JavaScript based on Vitest library

### Setup

Setting up Vitest in NPM scripts:

	"scripts": { "test": "vitest" }

Adding"--globals" flag allows usage of some basic functions (it, expect) without additional imports.

Adding "--reporter verbose" provides more detailed output after running tests.

Adding "--run" flag means that tests will only run once (on npm test) - as an alternative to watching tests:

	"scripts": { "test:watch": "vitest" }

Test files should be created as 'foo.test.js' or 'foo.spec.js'.

### Example of most basic unit test:

	import { it, expect } from 'vitest'; // instead of 'it', we can use 'test'
	import { add } from './math'; // our function (unit) to test
	it('should summarize all number values in an array', () => {
	    // Arrange Phase
	    const numbers = [1,2,3];
	    // Act Phase
	    const result = add(numbers);
	    // Assert Phase
	    const expectedResult = numbers.reduce(
	        (prevValue, curValue) => prevValue + curValue, 
	        0
	    );
	    expect(result).toBe(expectedResult); // expect can chain many functions
	});

### Example of unit test that checks if error is thrown:

	it('should throw an error if no value is passed into the function', () => {
	    const resultFunction = () => {
	        add();
	    };
	    expect(resultFunction).toThrow();
	});

We can also expect a specific error, i.e. use regex to check error msg:

	expect(resultFunction).toThrow(/is not iterable/);

Expect can also be reverted by chaining .not:

	expect(resultFunction).not.toThrow();

### Test suites

Unit tests can be grouped using describe() function. This function takes a description (i.e. name of function thath we test) and a callback with all related tests.

	describe('validateStringNotEmpty()', () => {
	    // all tests related to this function go in here
	});

### Asynchronous test - callback example

To run async test correctly, we invoke done() - testing engine will know that assertion (async) is ready and it should perform expect().

	it('should generate a token value', (done) => {
	    const testUserEmail = 'test@test.com';
	    generateToken(testUserEmail, (err, token) => {
	        expect(token).toBeDefined();
	        done();
	    });
	});

### Asynchronous test - promise example

In case of testing a function that returns a Promise, we can chain 'resolves' or 'rejects' to expect(). Notice that we should return expect().

	it('should generate a token value', () => {
	    const testUserEmail = 'test@test.com';
	    return expect(generateTokenPromise(testUserEmail)).resolves.toBeDefined();
	});

As an alternative, we can use async await:

	it('should generate a token value', async () => {
	    const testUserEmail = 'test@test.com';
	    const token = await generateTokenPromise(testUserEmail);
	    expect(token).toBeDefined();
	});

### Hooks

Hooks are functions executed automatically by test runner at certain points. Vitest hooks include: beforeAll, beforeEach, afterEach, afterAll. These function can be executed to register other functions, that should be executed before/after all/every single tests run. They can be applied to a suite (if called inside a suite) or all tests in a file (if called outside suites).

	beforeAll(() => {
	  console.log('beforeAll run');
	});

Example use cases:
- we can use beforeAll to run some common initialization.
- we can use afterEach to reset some value after each test, so that changed made to that value inside specific test will not affect other tests.
- afterAll can be used to perform some clean up work.

### Concurrent tests

Vitest allows running concurrent test, which can speed up overall testing process. By default all tests INSIDE SINGLE FILE run after each other. To use concurrent testing, we chain 'concurrent' to 'it':

	it.concurrent('should do something', () => {
	});

Concurrent can also be added to suite (describe.concurrent()).
A downside of concurrent execution is, that tests that perform clashing (global) state manipulations may interfere with each other.

### Side effects and Mockups

Side effects can occur i.e. when we test a function that writes to the filesystem or database. To deal with unwanted side effects we use spies and mockups.

Spies are 'wrappers' around functions or empty replacements for functions that allow you  to track if & how a function was called.

In the example below, we create a 'fake' empty function (spy) that we pass to our tested function and check if it was executed. 'vi' object is imported from vitest.

    	it('should execute logFn if provided', () => {
	        const logger = vi.fn();
	        generateReportData(logger);
	        expect(logger).toBeCalled();
	    })

We can replace default empty function with custom implementation for a certain test like this:

	        const logger = vi.fn();
	        logger.mockImplementationOnce(() => {});

Mocks are a replacements for an API that may provide some test-specific behavior instead. We can use 'vi' object (from vitest) to create an 'empty' replacement for a certain module and than check if some method on that module was executed. Test engine replaces all methods of that module with empty functions. In the example below, we mock fs (filesystem) module:

	import { it, expect, vi } from 'vitest';
	import { promises as fs } from 'fs';
	vi.mock('fs')
	it('should execute the writeFile method', () => {
	    const testData = 'Test';
	    const testFilename = 'test.txt';
	    writeData(testData, testFilename);
	    expect(fs.writeFile).toBeCalled();
	});

We can also create our own replacement (i.e. to check what happens to certain arguments), by passing second argument to mock method. The second argument should be a function that returns an object - that object will be our replacement.

	vi.mock('path', () => {
	    return {};
	});

Mocked modules can be kept in a special '__mocks__' directory. If Vitest find our own implementation in that directory, it will use this instead of simply creating its own implementation with empty functions.

To mock a globally available modules (i.e. browser built-in, like fetch), we use stubGlobal method, passing desired module's name as a string. The second value is our replacement.

	vi.stubGlobal('fetch', () => {});

### Testing the DOM

To emulate running on a browser, JSDOM or Happy-DOM can be used, provided by Vitest (Happy-DOM is Vitest only). In the examples below we use Happy-DOM.

Enviornment can be enabled in package.json by adding --enviornment flag:

 	"scripts": {
	    "start": "http-server -c-1",
	    "test": "vitest --run --environment happy-dom"
	  }

Then we can set up emulated html document to be used in our test:

	import fs from 'fs';
	import path from 'path';

	import { it, expect, vi } from 'vitest';
	import { Window } from 'happy-dom';
	import { showError } from './dom';

	const htmlDocPath = path.join(process.cwd(), 'index.html');
	const htmlDocumentContent = fs.readFileSync(htmlDocPath).toString();

	const window = new Window();
	const document = window.document;
	document.write(htmlDocumentContent);
	vi.stubGlobal('document', document);

If our tests make changes to virtual DOM, we can perfrom a cleanup:

    beforeEach(() => {
        document.body.innerHTML = '';
        document.write(htmlDocumentContent); // rewrite document before each to cleanup changes
    });