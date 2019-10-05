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