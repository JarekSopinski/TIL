## React Testing Library

### RTL vs Jest / Vitest

RTL
- Provides simulated DOM for tests
- Provides ways to manipulate and examine simulated DOM

Jest / Vitest
- Test runners that find tests, run tests and determines wheter they pass or fail.

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
- Functional tests - tests a particular function of software (not testing code, but testing behaviour)
- E2E tests - use actual browser and server

### Functional testing

RTL promotes functional testing.

Unit testing - isolated, mock dependencies, test internals
Prones: very easy to pinpoint failures
Cones: Further from how users interact with software, more likely to break with refactoring

Functional testing - include all relevant units, test behavior
Prones: close to how users interact with software, less likely to break with refactoting
Cones: More difficult to debug failing tests

### Accessibility and finding elements

RTL recommends fiding elements by accessibility handles.
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

### Example of test searching for text in a component

    import { render, screen } from '@testing-library/react';
    import App from './App';

    test('App contains correct heading', () => {
        render(<App />);
        const headingElement = screen.getByText(/learn react/i);
        expect(headingElement).toBeInTheDocument();
    })