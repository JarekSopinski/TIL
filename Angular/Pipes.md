## Angular Pipes

In Angular Pipes are used to format data. We can create our own Pipes and use built-in pipes, like: Uppercase, Lowercase, Decimal, Currency or Percent.

In this example we display "title" in uppercase:

    title = "lorem ipsum";
    <h1>{{ title | uppercase }}</h1>

Pipes can be chained like this:

    <h1>{{ title | uppercase | lowercase }}</h1>

Pipes can take arguments, written in a string after a colon. For example, the Number pipe can be set to display a minimum and maximum number of digits after decimal point:

    rating = 4.9745;
    {{ foo.rating | number:'1.2-2' }} // 2 as min and max, result is 4.97

A single Pipe can have multiple arguments, each passed after a colon:

    price = 190.95;
    {{ price | currency:'USD':true:'3.2-2' }}

### Custom Pipes

Let's say that we want to create a Summary Pipe that will shorten a string by a certain numer of characters.

First we have to create a file named summary.pipe.ts. This is Angular's naming convention for Pipe files. Inside this file we import the Pipe decorator function and PipeTransform - an interface that defines a shape of all Pipes in Angular:

    import { Pipe, PipeTransform } from '@angular/core';

Now we create a class for our new Pipe. This class has to implement PipeTransform interface, which requires the transform() method. This method takes two arguments: a value (required) and args (optional). Inside this method we can write our shortening algorithm. We also have to aply the @Pipe decorator, which sets the name (reference) for our Pipe:

    @Pipe({
        name: 'summary'
    })

    export class SummaryPipe implement PipeTransform {

        transform(value: string, args?:any) {
            if (!value)
                return null;

            return value.substr(0, 50) . '...';
        }

    }

Before we can use this Pipe in a component, we have to register it in a module, by adding it to module's declarations:

    import { SummaryPipe } from './summary.pipe';

    @ngModule({
        declarations: [
            //...
            SummaryPipe
        ]
    })

Now let's add a parameter - a number of characters by which a string will be shortened. This parameter will be called 'limit'.

    export class SummaryPipe implement PipeTransform {

        transform(value: string, limit?:number) {
            if (!value)
                return null;

            let actualLimit = (limit) ? limit : 50;
            return value.substr(0, actualLimit) . '...';
        }

    }

In the above example transform() can use a limit set in a component (actualLimit), but since limit param is optional, it can fall back to the default limit of 50.