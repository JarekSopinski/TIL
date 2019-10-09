## Angular components

Each Angular application consits of modules, that can be resposible for larger functionalities (i.e. you can have an admin panel module). Modules are than divided into components, which are responsible for views and logic of a certain code blocks.

Components can be created manually or generated using Angular CLI. In the second case, we use "ng g c foo" command, which stands for "ng generate component". In this example, "foo" is a component's name.

In the main component file (i.e. "foo.component.ts") we use Angular @Component decorator to set component's properties, including its selector (by which name it'll be included in other places), a template html (or a path to an outside html file responsible for view) and a CSS file url. Than we have to export component's class and add it to its module declarations.

In the component's main .ts file:

    import { Component } from '@angular/core';

    @Component({
      selector: 'foo',
      templateUrl: './foo.component.html',
      styleUrls: ['./foo.component.scss']
    })
    export class FooComponent {
    }

In the module:

    @NgModule({
      declarations: [
        AppComponent,
        FooComponent
      ],
      // ...
    })

Of course, if we use "ng g c" command, we don't have to write the above code manually.

An Angular component should not contain any logic other than the presentaion (view) logic. We should never run any http requests inside a component - [services](https://github.com/JarekSopinski/TIL/blob/master/Angular/Services.md) are responsible for that.

### Sending data to the view

There are several ways for data to be displayed in a component's view (html template).

#### String interpolation

This referes to displaying data in double curly bracket, which is actually a "syntax sugar" for property binding - explained below.

    <h1>{{ title }}</h1>

#### Property binding

A way of binding DOM properties to component's data, i.e.:

    <img [src]="imageUrl" >

#### Class binding

A way of adding classes dynamicly, if a certain condition has been met:

    <button class="btn btn-primary" [class.active]="isActive" >Save</button>

    export class FooComponent {
      isActive = true;
    }

In this case "active" class will be added only if isActive variable is truthy.

#### Style binding

A variation of property binding, used to dynamicly set the value of style property, i.e.:

    <button [style.backgroundColor]="isActive ? 'blue' : 'white'"></button>

### Event binding

To send data in a different direction (from the view) we use event binding.

Below is a simple example of triggering console log by clicking a button binded to the onSave() method:

    <button (click)="onSave()">Save</button>

    export class FooComponent {
      onSave() {
        console.log('button was clicked');
      }
    }

#### Accessing event object

To get access to the event object, we have to add this as a "$event" parameter to the method. Notice the dollar sign.

    <button (click)="onSave($event)">Save</button>

    export class FooComponent {
      onSave($event) {
        console.log($event);
      }
    }

We can also use stopPropagation() method with the $event parameter:

    export class FooComponent {
    onSave($event) {
      $event.stopPropagation();
      console.log($event);
    }
  }

#### Event filtering

Events can be filtered, i.e. to trigger button press event only for certain button:

    <input (keyup.enter)="onKeyUp()" />

In the above example event will be triggered only upon pressing enter button.

#### Template variables

Let's say that we want to access an input's value. A standard way would be to access a property of $event object:

    <input (keyup.enter)="onKeyUp($event)" />

    onKeyUp($event) {
      console.log($event.target.value);
    }

In Angular, instead of using an $event object, we can declare a variable that references an input field. This is called a template variable. Below "#email" is an example of a template variable. Notice the "#" sign at the beginning.

    <input #email (keyup.enter)="onKeyUp(email.value)" />

    onKeyUp(email) {
      console.log(email);
    }

### Two-way data binding

Two-way data binding is a way to simplify passing data in both ways. It eliminates the need for additional parameters, like in above examples. We can modify them like this:

    <input [value]="email" (keyup.enter)="$event = $event.target.value;   onKeyUp()" />

    export class FooComponent {
      email = 'me@example.com'; // initial value

      onKeyUp() {
        console.log(this.email);
      }
    }

Now, we first set email value, and than call onKeyUp() method. So this method will display value passed to the input. 

#### Using ngModel

Of course, the above code isn't very clean. We can use Angular's ngModel directive to implement two-way data binding in a more simple way:

    <input [(ngModel)]="email" (keyup.enter)="onKeyUp()" />

Now we don't have to write additional code for setting the value, we only call onKeyUp() method.

Of course, ngModel has to be imported from the Forms module. We add it  to imports in component's parent module @NgModule decorator:

    import { FormsModule } from '@angular/forms';
  
    @NgModule({
      //...
      imports: [
        BrowserModule,
        FormsModule
      ]
    })