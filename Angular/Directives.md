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

We can also use values exported from ngFor after saving them to local variables. I.e. if we want to render an index of the item:

    <ul>
        <li *ngFor="let item of items; index as i">
            {{ i }} - {{ item }}
        </li>
    </ul>

We can also check if an item is even, odd, first or last. Full list of exported values can be found [here, under "Local variables".](https://angular.io/api/common/NgForOf)

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

### ngSwitch

If we want to compare a property or a field against multiple values, we can use ngSwitch. In the example below it checks a value of "viewMode" and decides witch of the three div items should be rendered. Notice thath insise ngSwitchCase we fire have "" for an expression and then '' for the value ("''").

    <div [ngSwitch]="viewMode">
        <div *ngSwitchCase="'map'">Map View Content</div>
        <div *ngSwitchCase="'list'">List View Content</div>
        <div *ngSwitchDefault>Otherwise</div>
    </div>

### ngClass

ngClass directive can be a cleaner alternative for class binding. Below is an example that shows how ngClass can replace class binding.

    // class binding
    <span
        class="glyphicon"
        [class.glyphicon-star]="isSelected"
        [class.glyphicon-star-empty]="!isSelected"
    ></span>

    // ngClass
    <span
        class="glyphicon"
        [ngClass]="{
            'glyphicon-star': isSelected,
            'glyphicon-star-empty': !isSelected
        }"
    ></span>

### ngStyle

ngStyle works in a similar way to ngClass:

    [ngStyle]="{
        'backgroundColor': canSave ? 'blue' : 'gray',
        'color': canSave ? 'white' : 'black'
    }"

Both ngStyle and ngClass are examples of attribute directives.