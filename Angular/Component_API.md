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