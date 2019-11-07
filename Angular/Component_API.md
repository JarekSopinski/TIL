## Component API

Angular components can be reusable with the usage of input and output properties. We use input properties to pass data to the component and output properties to raise events from this component. Their combination is an component API.

In the examples below we have FavoriteComponent, with its own API, and AppComponent, which implements FavoriteComponent as a reusable component and make use of its API.

### Input properties

#### Adding input property with the Input decorator

In FavoriteComponent:

    import { ... Input} from '@angular/core';

    export class FavoriteComponent {
        @Input isFavorite: boolean;
        ...
    }

In AppComponent:

    export class AppComponent {
        post = {
            isFavorite: true
        }
        ...
    }

    <favorite [isFavorite]="post.isFavorite"></favorite>

#### Adding input property as component's metadata

An alternative way without importing Input decorator.
In FavoriteComponent:

    @Component({
        ...
        inputs: [isFavorite]
    })

This creates isFavorite property 'under the hood', that would look like this:

    export class FavoriteComponent {
        isFavorite: boolean
        ...
    }

This way has a disadvantage of creating 'magic string' that can cause problems during refactoring. So its better to use first approach.

#### Aliasing input properties

We can give aliases ('nicknames') to input properties.

In FavoriteComponent:

    export class FavoriteComponent {
        @Input('is-favorite') isFavorite: boolean; // 'is-favorite' is our alias
        ...
    }

In AppComponent:

    <favorite [is-favorite]="post.isFavorite"></favorite>  

Now in FavoriteComponent we can change it name (isFavorite), but as long as alias stays the same, we don't have to update anyting in other components that use FavoriteComponent.

### Output properties

#### Adding output property with the Output decorator

In AppComponent:

    export class AppComponent {
        onFavoriteChanged(){
            console.log('Favorite changed');
        }
    }

    <favorite (change)="onFavoriteChanged()"></favorite>

In FavoriteComponent:

    import { ... Output, EventEmitter} from '@angular/core';

    export class FavoriteComponent {
        @Output change = new EventEmitter(); // change is the name of our (new instance of) EventEmitter
        onClick(){
            this.change.emit; // 'emit' method raises an event - notifies, that an event happend
        }
    }

#### Passing event's data with an output property

We can pass data to event's subscriber (in this case AppComponent). When event is emitted, we can pass it some value, that will be availabe to the subscriber. This value sets a new state of the component.

In this example 'change' is the name of output property.

In FavoriteComponent:

    export class FavoriteComponent {
        @Output change = new EventEmitter();
        onClick(){
            this.isSelected = !this.isSelected;
            this.change.emit({ newValue: this.isSelected });
        }
    }

In AppComponent:

    export class AppComponent {
        onFavoriteChanged(eventArgs){
            console.log(eventArgs.newValue);
        }
    }

    <favorite (change)="onFavoriteChanged($event)"></favorite>
    // $event can be anything we pass while raising an event

#### Using interface for event's data

It's a good practice to use an interface, that will define shape of eventArgs object.

In FavoriteComponent:

    export interface FavoriteChangedEventArgs {
        newValue: boolean
    }

In AppComponent:

    import { FavoriteChangedEventArgs } from...

    export class AppComponent {
        onFavoriteChanged(eventArgs: FavoriteChangedEventArgs){
            console.log(eventArgs.newValue);
        }
    }

#### Aliasing output property

As with input properties, output properties can have aliases.

In FavoriteComponent:

    export class FavoriteComponent {
        @Output('change') changeNewName = new EventEmitter();
        onClick(){
            this.isSelected = !this.isSelected;
            this.changeNewName.emit({ newValue: this.isSelected });
        }
    }

In AppComponent output will still work under 'change', event after the name was changed to changeNewName in FavoriteComponent.

    <favorite (change)="onFavoriteChanged($event)"></favorite>