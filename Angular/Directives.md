## Angular Directives

In Angular Directives are used for DOM manipulation, i.e. removing elements, adding elements, changing classes.

### ngFor

ngFor is used for iterating over an array. Let's say that in our component we have an array of items:

    items = ["item1", "item2", "item3"]

Than in the template file we can use ngFor to create an ul list from this array:

    <ul>
        <li *ngFor="let item of items">
            {{ item }}
        </li>   
    </ul>