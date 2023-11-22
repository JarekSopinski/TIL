## TypeScript Generics

### Built-in generics

Array or Promise are examples of generics built into TS:

    const arr: Array<string>
    const promise: Promise<string>

### Example generic function

In the case below, we tell TS that the function will have two arguments of different types - and that it will return the intersection of them.

By a convention, we use 'T' for first parameter, and then next letters from alphabet.

    function merge<T extends Object, U extends Object>(objA: T, objB: U) {
        return Object.assign(objA, objB);
    }

    const mergedObj = merge({name: 'John'}, {age: 100});
    console.log(mergedObj.name); // John

Notice that by using 'extends Object', we force the implementation of any type that is an object. So, for example, passing a string under T or U would cause an error.

If we would write it like (objA: object, objB: object), we wouldn't be able to later call specific props of merged object. Because TS would guess that we return any object, not intersection of objA and objB.

    function mergeWrong(objA: object, objB: object) {
        return Object.assign(objA, objB);
    }

    const mergedObjWrong = mergeWrong({name: 'John'}, {age: 100});
    console.log(mergedObjWrong.name); // Error: property name does not exist on type object

### keyof constraint

Another constraint, besides 'extends', is 'keyof'. In the example below we make sure that 'U' is a key of 'T' object.

    function extractAndConvert<T extends object, U extends keyof T>(obj: T, key: U) {
        return 'Value: ' + obj[key];
    }

    console.log(extractAndConvert({ name: 'John' }, 'name')); // Value: John
    // console.log(extractAndConvert({ name: 'John' }, 'foobar')); // Error

### Generic classes

In the example below we define DataStorage class, that will keep any type of data. Than we create implementations of that class, specifying which type of data each implementation will accept.

    class DataStorage<T> {
        private data: T[] = [];

        addItem(item: T) {
            this.data.push(item);
        }
    }

    const textStorage = new DataStorage<string>();
    textStorage.addItem('John'); // Correct
    // textStorage.addItem(10); // Error: Argument of type 'number' is not assignable to parameter of type 'string'

    const numbersStorage = new DataStorage<number>();
    numbersStorage.addItem(10); // Correct