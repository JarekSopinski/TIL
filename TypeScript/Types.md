### Core Types

- any (any kind of value, no specific assignment)
- number (all numbers, no differentiation between integers or floats)
- string
- boolean
- object (any JS object, more specific types (type of object) are possible)
- array (any JS array, type can be flexible or strict ( regarding the element types, i.e. string[] ))
- tuple (added by TS: fixed-length and fixed-type array, i.e. role:[number, string])
- enum (added by TS: auto enumerated global constant identifiers, i.e. enum { NEW, OLD })

The core primitive types in TS are all lowercase, as opposing do JS, i.e. 'string', not 'String'.

### Type inference

TS tries to understand by itself which type will be assigned to a variable. Therefore if a variable is initialized with a certain value, there is no need to manually assign a type.

    let number: number; // good
    let number: number = 5; // redundant

### Enum

Example of enum is TS:

    enum Role { ADMIN, USER, AUTHOR };
    let role = Role.ADMIN; // 0
    let rol2 = Role.USER; // 1

Default enum starts from 0, but we can manually change this:

    enum Role { ADMIN = 1, USER, AUTHOR = 999 };
        let role = Role.ADMIN; // 1
        let role2 = Role.USER; // 2 - auto increment from 1
        let role3 = Role.AUTHOR // 999 - but we can also break incrementation

# Union

Union allows multiple data types.

    foo: number | string | boolean

### Literal

Literal types specifies not only data type, but also the value itself.

    foo: 'foobar' | 'zoobar' | 'roobar'

### Type Aliases

Type aliases can save declaration of some type, useful if working with unions:

    type NumberOrString = number | string;
    foo: NumberOrString;

...or literals:

    type ConversionDescriptor = 'as-number' | 'as-text';

### Function return types and void

Function can have defined return type. In most cases this will be done automatically through inference. Therefore usually we don't have to manually set function's return type.

    function add(n1:number, n2:number): number {
        return n1 + n2;
    }

If a function doesn't return, then its return type will be set to 'void'.

    function printResult(num: number): void {
        console.log('Result ') + num;
    }

### Functions as types

We can set variable to a function type:

    let combineValues: Function;
    combineValues = add;
    combineValues(8,8);

In the above example, any function will be allowed. But we can also be more specific, regarding allowed arguments and return type:

    let combineValues: (a:number, b:number) => number;

In the above example, only a function that takes two numbers and returns a number will be allowed.

In a similar way we can specify callbacks:

    function addAndHandle(n1: number, n2: number, callback: (num: number) => void) {
        const result = n1 + n2;
        callback(result);
    }

    addAndHandle(10, 20, (result) => {
        console.log(result);
    });

### 'Unknown' type

'Unknown' describes a variable which type is not yet known, but will be known in the future. It's similar to 'any' but a bit less flexible. Opposing to 'any', it can't be passed to a variable that expects a certain type.

    let userInput: unknown;

    userInput = 5; // ok
    userInput = 'Foo'; // ok

    let userName: string;
    userName = userInput; // compilation error - userName expects string!

### Never

'Never' is a newer type that can describe a return type of a function, that is intended never to return anything, i.e. because it throws an error.

    function generateError(message: string, code: number): never {
        throw {message: message, errorCode: code};
    }

    generateError('An error occurred!', 500);