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