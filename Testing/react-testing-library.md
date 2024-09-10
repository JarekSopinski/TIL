## React Testing Library

### RTL vs Jest / Vitest

RTL
- Provides simulated DOM for tests
- Provides ways to manipulate and examine simulated DOM

Jest / Vitest
- Test runners that find tests, run tests and determines whether they pass or fail.

Jest and Vitest have almost identical syntax, they only have different setup.

### TDD

Write tests before writing code
    - then write code according to 'spec' set by tests

1. Create an empty React component ('shell' function)
2. Write tests
3. Tests fail - as expected, because the function does not do anything
4. Write actual code that is required for tests to pass
5. Tests pass!

### Types of tests

- Unit tests - tests one unit of code in isolation
- Integration tests - how multiple units work together
- Functional tests - tests a particular function of software (not testing code, but testing behavior)
- E2E tests - use actual browser and server

### Functional testing

RTL promotes functional testing.

Unit testing - isolated, mock dependencies, test internals
Prones: very easy to pinpoint failures
Cones: Further from how users interact with software, more likely to break with refactoring

Functional testing - include all relevant units, test behavior
Prones: close to how users interact with software, less likely to break with refactoring
Cones: More difficult to debug failing tests

### Accessibility and finding elements

RTL recommends finding elements by accessibility handles.
https://testing-library.com/docs/queries/about/#priority

Role definitions: https://www.w3.org/TR/wai-aria/#role_definitions

### How does Jest / Vitest work?

They provide global 'test' method, that has two arguments:
- string description of a test
- test function

Test fail when test function throws an error. So an empty test will still pass!

### Assertions

All assertions start with 'expect', which is a global in Jest/Vitest.
We provide expect argument (subject of assertion) - i.e. 'linkElement'.
Then we add matcher (type of assertions), from Jest-DOM.

    expect(linkElement).toBeInTheDocument();
    expect(elementsArray).toHaveLength(7);

### Recommended ESLint Plugins

https://github.com/testing-library/eslint-plugin-testing-library
https://github.com/testing-library/eslint-plugin-jest-dom
https://github.com/veritem/eslint-plugin-vitest

### Example of test searching for text in a component

    import { render, screen } from '@testing-library/react';
    import App from './App';

    test('App contains correct heading', () => {
        render(<App />);
        const headingElement = screen.getByText(/learn react/i);
        expect(headingElement).toBeInTheDocument();
    })

### Logging roles

If we're unsure about roles in the component, we can log them like this. Roles will be logged in the console upon running npm test.

    const { container } = render(<App />);
    logRoles(container);

### Simple example of functional test - clicking a button and changing color

For events, we can use fireEvent function, which can be imported from '@testing-library/react'.

    test('buttons click flow', () => {
        // render App
        render(<App />);

        // find the button
        const buttonElement = screen.getByRole('button', {name: /blue/i});

        // check initial color
        expect(buttonElement).toHaveClass('red');

        // click the button
        fireEvent.click(buttonElement);

        // check button text
        expect(buttonElement).toHaveTextContent(/red/i);

        // check button color
        expect(buttonElement).toHaveClass('blue');
    });

### userEvent vs fireEvent

Another way for firing events is user-event, which simulates full interaction. This is recommended way to test events.

userEvent is provided by additional package @testing-library/user-event.

It requires starting a session with a setup() method.
Notice that userEvent APIs always return a Promise. So they must be awaited.

    const user = userEvent.setup();
    await user.click(element);

### screen Query Methods

Screen query methods (finding els in DOM) are constructed like this:
    
    command[All]ByQueryType

    screen.getByText('foobar');
    screen.queryByText('foobar');

command
- get: expect element to be in DOM
- query: expect element NOT to be in DOM
- find: expect element to appear async

All
- exclude: expect only one match
- include: expect more than one match

QueryType
- Role
- AltText
- Text
- Form elements (PlaceholderText, LabelText, DisplayValue)

### Mock Service Worker

This is a tool for running tests on mocked API responses.

#### Purpose
- intercept network calls
- return specified responses
- Prevents actual network calls during tests
- Set up tests conditions using server responses

#### To setup MSW:

    npm install msw

1. Create handlers

        // src/mocks/handlers.js
        import { http, HttpResponse } from 'msw';

        export const handlers = [
          http.get('/resource', () => {
                return HttpResponse.json({
                    ...some mocked data
                });
          }),
        ]

2. Create test server

        // src/mocks/node.js
        import { setupServer } from 'msw/node'
        import { handlers } from './handlers'

        export const server = setupServer(...handlers)

3. Make sure test server listens during all tests. Server handlers should be reset after each test.

        // vitest.setup.js
        import { beforeAll, afterEach, afterAll } from 'vitest'
        import { server } from './src/mocks/node'

        beforeAll(() => server.listen())
        afterEach(() => server.resetHandlers())
        afterAll(() => server.close())

### Using findBy on async tests

When you are waiting for something to appear asynchronously on the page, you must use

    await findBy

For example:

    const loadedImages = await screen.findAllByRole('img');

### Testing async errors

If we want to test errors, we can override default handlers in a test:

    import { http, HttpResponse } from 'msw';
    import { server } from '../../../mocks/server';

    test('handles server error', async () => {

        server.resetHandlers(
            // Notice that here we're NOT passing an array!
            http.get('http://localhost:3030/foobar', () => {
                return new HttpResponse(null, { status: 500 })
            }),
            http.get('http://localhost:3030/zoobar', () => {
                return new HttpResponse(null, { status: 500 })
            }),
        );

        //... rest of the test, i.e. how component will react to error...
    });

### Debugging

#### Restricting tests

Press 'p' in the console (where tests are running), than type a name of a test file you want to filter by. Tests will run only for this file. It's then easier to debug this certain test.

We can also add .only to a certain test - only this test in the file will run.

    test.only('foobar', () => {});

Alternatively, we can skip:

    test.skip('foobar', () => {});

### Useful links

https://testing-library.com/docs/react-testing-library/cheatsheet/