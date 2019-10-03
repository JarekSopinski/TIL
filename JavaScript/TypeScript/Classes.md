## TypeScript Classes

An example of a class with x and y properties and draw() method:

    class Point {
        x: number;
        y: number;

        constructor(x: number, y: number) {
            this.x = x;
            this.y = y;
        }

        draw() {
            console.log('X: ' + this.x + ', Y: ' + this.y);
        }

    }

    let point = new Point(1,2);
    point.draw();

If we would like x and y to be optional, we can write them in a constructor like this:

    constructor(x?: number, y?: number) {
        this.x = x;
        this.y = y;
    }

## Access modifiers

In TypeScript we have "public", "private" and "protected" access modifires. Note that they are not available in "pure js" ES6 classes.

By default all properties are public.

We can set properties to private like this:

    class Point() {
        private x: number;
        private y: number;
        //...
    }

    let point = new Point(1,2);
    point.x = 3; // error

## Simplifying class initialization

In TypeScript we can write class initialization in a more simple way. First, we don't need to declare properties before the constructor, because compiler will create them for us. Moreover, access modifiers can be written inside a constructor. So, we can modify previous example like this:

    class Point {
        constructor (private x?: number, private y?: number) {}
        //...
    }

## Accessing private properties

Below is an example of using getter and setter for private properties. Note that prefixes ("_") were added to properties name to prevent name conflict.

    class Point {

        constructor(private _x?: number, private _y?: number) {
        }

        draw() {
            console.log('X: ' + this._x + ', Y: ' + this._y);
        }

        get x() {
            return this._x;
        }

        set x(value) {
            if (value < 0)
                throw new Error('value cannot be less than 0');
            this._x = value;
        }

    }

    let point = new Point(1,2);
    let x = point.x; // using a getter
    point.x = 10; // using a setter
    point.draw();