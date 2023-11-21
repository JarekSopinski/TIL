## Advanced types

### Intersection types

Intersection types allow us to combine other types.

In case of objects, they behave like extending interfaces:

    type Admin = {
        name: string;
        privileges: string[];
    };

    type Employee = {
        name: string;
        startDate: Date;
    };

    type ElevatedEmployee = Admin & Employee;

In case of unions, they use only common types (in the example below, Universal will be number):

    type Combinable = string | number;
    type Numeric = number | boolean;

    type Universal = Combinable & Numeric;

### Discriminated union pattern

This pattern can be used for type guarding, by adding common property that helps with identifying interfaces.

    interface Bird {
        type: 'bird';
        flyingSpeed: number;
    }

    interface Horse {
        type: 'horse';
        runningSpeed: number;
    }

    type Animal = Bird | Horse;

    function moveAnimal(animal: Animal) {
        let speed;
        switch (animal.type) {
            case 'bird':
                speed = animal.flyingSpeed;
                break;
            case 'horse':
                speed = animal.runningSpeed;
                break;
        }
        console.log('Moving at speed ' + speed);
    }

### Type casting

A way of telling TS that some value is of specific type, when it cannot detect it by itself.

Type HTMLElement as HTMLInputElement:

    const userInputElement = <HTMLInputElement>document.getElementById('message-output');

The same with alternative syntax:

    const userInputElement = document.getElementById('message-output') as HTMLInputElement;

Or:

    const userInputElement = document.getElementById('message-output');
    const inputValue = (userInputElement as HTMLInputElement).value

### Index properties

A way do create flexible interfaces or types. In the example below we can add flexible properties - anyting that can be converted to a string and have a string value.

    interface ErrorContainer {
        [prop: string]: string;
    }

    const errorBag: ErrorContainer = {
        email: 'Not a valid email!',
        username: 'Must start with a capital character'
    }

### Function overloads

A feature that allows multiple function signatures for one function. We can define what will be the return type, depending of types of provided arguments.

    function add(a: number, b: number): number; // if both args are numbers, than it returns number
    function add(a: string, b: string): string; // if both args are strings, than it returns string
    function add(a: string | number, b: string | number) {
        if (typeof a === 'string' || typeof b === 'string') {
            return a.toString() + b.toString();
        }
        return a + b;
    }

    const resultNumber = add(1, 5); // type number
    const resultString = add('Adam', 'Smith'); // type string
    resultString.split(' '); // no type error

### Optional chaining

Secure way to get nested properties, that can be undefined.

    type UserData = {
        id: string,
        name: string,
        job?: { title: string, description: string }
    }

    const fetchedUserData: UserData = {
        id: 'u1',
        name: 'Max',
        // job: { title: 'CEO', description: 'My own company' }
    };

    console.log(fetchedUserData?.job?.title);

### Null coalescing

Default values that are ONLY null or undefined (not falsy, like empty string or 0).

    const userInput = null;
    const storedData = userInput ?? 'DEFAULT';