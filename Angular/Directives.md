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

### ngIf

ngIf renders an element if condition passed to it evaluates to true:

    <div *ngIf="items.length > 0">
        Some items
    </div>

We can also create else-if condition blocks using ng-templates and specific keywords('then', 'else'). In the example below ngIf checks an condition and decided which ng-template block should be rendered:

    <div *ngIf="items.length > 0; then itemsList else noItems"></div>
    <ng-template #itemsList>
        List of items
    </ng-template>
    <ng-template #noItems>
        No items yet
    </ng-template>

An alternative way of conditionally hiding an element can be to bind a condition to a hidden attribute:

    <div [hidden]="items.length === 0">
        List of items
    </div>

    <div [hidden]="items.length > 0">
        No items yet
    </div>

The main difference between this to approaches is the fact that ngIf removes an element from the DOM, while switching hidden attribute only hides it.