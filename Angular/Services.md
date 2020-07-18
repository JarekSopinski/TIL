## Angular Services

Because Angular components should only be responsible for view logic, any other logic - mainly handling http requests - has to be moved somewhere else. For this, we have services, where get data from the server and send it back.

By a convention service's name should contain the world "service", i.e. "foo.service.ts", where "foo" is a specific name of this service.

In contrast to the components, at the beginning of a service's file we don't use Angular decorator. So the most basic service can look like this:

    export class FooService {
        getData() {
            //...
            return data;
        }
    }

### Adding service to a component (bad way)

Than, to use a service in a component, we have to create an instance of service's class in this component's constructor function:

    import FooService from ...

    export class FooComponent {
        items;
        constructor() {
            let service = new FooService();
            items = service.getData();
        }
    }

Now "items" data can be used in a FooComponent. But this implementation couses some problems. I.e. if service's constructor was modified, we would have to update every instance in every component where it was used. A solution is Angular's dependency injection, that implements a singleton design pattern.

### Adding service through a dependency injection (good way)

Instead of creating new instance of a service class in every component, that'll need to access its data, we can add this to providers in a module's decorator. Than Angular will create only one instance of this class that'll be accessible for all components in this module.

    @NgModule({
      declarations: [
        //...
      ],
      imports: [
        //...
      ],
      providers: [
        CoursesService
      ],
      bootstrap: [AppComponent]
    })
    export class AppModule { }

Than in components, instead of creating new instances of a service, we add it as a dependency to component's constructor:

    export class FooComponent {
        items;
        constructor(service: FooService) {
            items = service.getData();
        }
    }

We can also add a replacement to providers, if we want to use our own class instead of base Angular's class. In the example below, AppErrorHandler (custom class) replaces ErrorHandler (Angular class):

    providers: [
      { provide: ErrorHandler, useClass: AppErrorHandler }
    ],

### Generating a service with Angular CLI

We can use Angular CLI to automaticly generate a service by using a command:

    ng g s foo

In this example "g" stands for generate, "s" stands for service and "foo" is the name of the service.

This command will generate foo.service.ts file and foo.service.spec.ts file for testing. Note that this service won't be automaticly added to module's providers - we've to do it manually. Moreover, Angular will add Injectable decorator to service's imports, which would be needed if this services would have dependencies in its constructor.