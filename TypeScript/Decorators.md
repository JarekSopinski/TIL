## TypeScript decorators

Enable decorators in ts.config:

    "experimentalDecorators": true

Decorator is a applied to something, usually a class. It adds additional functionality to the class, without modifying class itself.
By a convention, decorators starts with capital letter.
Decorator accepts constructor as an argument.

    function Logger(constructor: Function) {
        console.log('Log from Logger');
        console.log(constructor);
    }

    @Logger
    class Person {
        name = 'Joe';
        constructor() {
            console.log('Log from Person');
        }
    }

    const person = new Person(); // Logs 'Log from Logger', 'Log from Person'

### Decorator factory

To allow adding custom arguments to decorator, we can create a decorator factory, by nesting decorator in another function. This function then returns the decorator.

    function Logger(logString: string) {
        return function(constructor: Function) {
            console.log(logString);
            console.log(constructor);
        }
    }
    
    @Logger('LOGGING - PERSON')
    class Person {
        name = 'Joe';
    
        constructor() {
            console.log('Log from Person');
        }
    }
    
    const person = new Person(); // Logs 'LOGGING - PERSON', 'Log from Person'

### Multiple decorators

In case of adding multiple decorators, they will execute from the bottom.

    @IRunSecond()
    @IRunFirst()
    class Foo {}