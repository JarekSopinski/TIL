## TypeScript interfaces

An interface is a way to define a "shape" of an object. This can be used to declare function parameters in a way that is better for preventing an error.

For example:

    interface Point {
        x: number,
        y: number
    }

    let drawPoint = (point: Point) => {
        // ...
    }

Accoring to a Pascal naming convention, each letter of interface's name should be uppercased.