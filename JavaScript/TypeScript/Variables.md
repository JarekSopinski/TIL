## TypeScript variables

### Data types declarations

Examples of variable declarations with various data types:

    let a: number;
    let b: boolean;
    let c: string;
    let d: any; // any data type
    let e: number[] = [1,2,3]; // array of numers with optional initialization
    let f: any[] = [1, true, 'a', false]; // array of any data types

If a data type was not declared, than the variable is any be default.

### Enum

Enums allow us to define a set of named constants. They can be numeric or string-based.

An example of numeric enum:

    enum Color { Red, Green, Blue };

This is the same as declaring:

    const Red = 0;
    const Green = 1;
    const Blue = 2;

A certain member can be accessed in a similar way to object's property:

    let backgroundColor = Color.Red;

This enum is automaticly incremented, but we can set its members values by ourselfes. It's better to do this to prevent errors:

    enum Color { Red = 0, Green = 1, Blue = 2 };

### Type assertions

Type assertions are a way to inform a compiler about a possible data type that it might not know - i.e. when we want to perform a string-based method on 'any' variable. It does not affect runtime.

There are two syntaxes for type assertions.

    let message; // variable is delcared as any - ts doesn't know that it might hold a string
    message = 'abc';
    // Below we basicly tell ts: "it's all right, it's a string":
    let endsWithC = (<string>message).endsWith('c'); // first syntax
    let alternativeEndsWithC = (message as string).endsWith('c'); // second syntax