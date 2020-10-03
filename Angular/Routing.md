## Angular - Routing

Routing is handled by Angular's Router module. 

    import { RouterModule } from '@angular/router';

There are 3 steps leading to implementing navigation:
1. Configure the routes - map paths to components.
2. Add a router outlet - display corresponding component, when a given route becomes active.
3. Add links.

### Configuring routes (module file)

We have to use static RouterModule.forRoot() method in our module's imports array. We use it to define app's routes. We pass it an array of routes. Each route is an object with 2 properties: a path and an component. 

Notice that none of the paths starts with a slash (/). For example, for component acting as a 'home page' we pass an empty string.

We can also add a parameter using colon, i.e. 'profile/:username'.

To use a 'wild cart' (i.e. for 404 error), we add two asterisks ('**').

An order of routes is important. I.e. a wild cart put at the beginning will catch any routes. More specific routes have to be put before less specific routes, i.e. 'users/:username' before 'users'.

    imports: [
      ...
      RouterModule.forRoot(
        { path: '', component: HomeComponent },
        { path: 'users/:username', component: UserProfileComponent },
        { path: 'users', component: UsersComponent },
        { path: '**', component: NotFoundComponent }
      )
    ],

### Adding router outlet (template file)

Router outlet is added to the template file - it represents the component added with a current route. This component is rendered after the outlet, not inside.

    <navbar></navbar>
    <router-outlet></router-outlet>

### Adding links (template file)

To add a link, in the place of href attribute we use routerLink directive, passing a correct path. If we'd have used href, the whole app would be reloaded on each link click.

For simple links, we pass a string representing desired path:

    <a class="nav-link" routerLink="/posts">Posts</a>

To add a link with a parameter, i.e. posts/1234, we use property biding syntax and set value to an array. The 1st property in this array is the path, while the next elements are route arguments:

    <a [routerLink]="['/posts', post.id]">{{ post.title }}</a>

To highlight an active link, we can use routerLinkActive directive. We pass it a string with css classes that should be applied when the link is active.

      <li routerLinkActive="active current" class="nav-item">
        <a class="nav-link" routerLink="/posts">Posts</a>
      </li>

### Getting route parameters

To access route parameters inside a component, we need to add ActivatedRoute class (part of Router module) in the constructor. Then we can access route.paramMap property. Note that paramMap is an observable, so we have to subscibe it to get its data.

In the returned params object, we can access two keys: 'keys' and 'params'. We can use methods: get(), getAll() and has() - to check if a param exists.

Let's imagine that we have a post component and we want to get the id of displayed post:

      export class PostComponent implements OnInit {
        constructor(private route: ActivatedRoute) { }
        ngOnInit() {
          this.route.paramMap
            .subscribe(params => {
              let id = params.get('id');
            })
        }
      }

If we are sure that the user will not be able to navigate back to current component and that the component will be destroyed, we can use a snapshot instead of subscribing to an observable:

      let id = this.route.snapshot.paramMap.get('id');

### Adding multiple parameters to the route

We can more than one param to the route in order to, let's say, display a seo friendly link.

    { path: 'users/:id/:name', component: UserProfileComponent },

    <a [routerLink]="['/users', user.id, user.name]">{{ user.name }}</a>

The result will be something like:

  ...users/1234/johndoe

### Adding optional query parameters

We can send optional query parameters in the template file, by binding to queryParams:

        <a 
          class="nav-link" 
          routerLink="/users"
          [queryParams]="{ page: 1, order: 'newest' }">Users</a>

Reciving query params is simillar to getting route params:

    this.route.queryParamMap.subscribe(); // using observable
    let page = this.route.snapshot.queryParamMap.get('page'); // using a snapshot

### Combining route paramters and query parameters by subscribing to multiple observables

To combine route parameters and query parameters, we can use combineLatest, provided by rxj, and subscribe to multiple observables (where route params is the first observable and query params is the second observable).

    import { combineLatest } from 'rxjs';

    combineLatest([
      this.route.paramMap,
      this.route.queryParamMap
    ]).subscribe(combined => {
      let id = combined[0].get('id'); // accessing route param
      let page = combined[1].get('page'); // accessing query param
    })

### Programmatic navigation

To navigate user programmaticly (i.e. redirecting on button click with query params) we can use navigate() method, available in Router.

    import { Router } from '@angular/router';
  
    //...
  
    constructor( private router: Router ) { }
  
    //...
  
    submit() {
      this.router.navigate(['/followers'], {
        queryParams: { page: 1, order: 'newest' }
      })
    }
  
    <button (click)="submit()" class="btn btn-primary">Submit</button>