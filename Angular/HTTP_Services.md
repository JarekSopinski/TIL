## Angular - consuming HTTP services

To use http services, HttpClientModule (previously HttpModule) has to be imported into our module:

    import { HttpClientModule } from '@angular/common/http';

### Performing basic CRUD operations

#### Getting data

To perform a GET request, we use a .get() method, which returns an Observable. We can subscribe to this Observable to get the response. There is no need to manually convert response into JSON.

    constructor(http: HttpClient){
        http.get('https://jsonplaceholder.typicode.com/posts')
        .subscribe(response => {
            console.log(response);
        })
    }

#### Posting data

In a similar way, we can perform a POST request. In this example we post object with data from an input field, than log the response.

    createPost(input: HTMLInputElement) {
      let post:object = { title: input.value }
      this.http.post(this.url, JSON.stringify(post))
        .subscribe(response => {
          console.log(response);
        })
    }

#### Updating data

To update data, we can use either PUT request or PATCH request; PATCH will send only a certain property, that should be modified. In this example, we send a PATCH request to modify isRead property of a clicked post:

    updatePost(post) {
      const changedPostUrl:string = `${this.url}/${post.id}`;
      this.http.patch(changedPostUrl, JSON.stringify({ isRead: true }))
        .subscribe(response => {
          console.log(response);
        })
    }

#### Deleting data

Delating is similar to updating, only that there is no need to send a body:

    deletePost(post) {
      const deletedPostUrl:string = `${this.url}/${post.id}`;
      this.http.delete(deletedPostUrl)
        .subscribe(response => {
          console.log(response);
          let index = this.posts.indexOf(post);
          this.posts.splice(index, 1);
        })
    }

### Using OnInit

If we want to get data when component is initialized, instead of using constructor, we should use OnInit - a lifecycle hook, that can perform some action upon component's initialization.

    export class PostsComponent implements OnInit {

      constructor(private http: HttpClient){
      }

      ngOnInit() {
        this.http.get(this.url)
          .subscribe(response => {
              // render response...
          })
      }

    }

### Using a service - separation of concerns

Handling Http calls directly in the component violates the separation of concerns principal. Instead we should create a service that will handle only Http calls, and then the component can use this serivce to handle views.

    export class PostService {
      private url: string = 'https://jsonplaceholder.typicode.com/posts';

      constructor(private http: HttpClient) { }

      getPosts() {
        return this.http.get(this.url);
      }

      createPost(post) {
        return this.http.post(this.url, JSON.stringify(post));
      }

      updatePost(post) {
        const changedPostUrl = `${this.url}/${post.id}`;
        return this.http.patch(changedPostUrl, JSON.stringify({ isRead: true }));
      }

      deletePost(id) {
        const deletedPostUrl = `${this.url}/${id}`;
        return this.http.delete(deletedPostUrl);
      }

    }

We can use the above in the component like this:

    export class PostsComponent implements OnInit {

      posts:any[] = [];

      constructor(private service: PostService){
      }

      ngOnInit() {
        this.service.getPosts()
          .subscribe(response => {
            // render response...
          })
      }

    }

### Handling Http errors

There are two types of Http errors: unexpected errors (server is offline, network is down, unhandled exceptions) and expected errors (404 not found, 400 bad request).

To handle unexpected error, we can add an error handler as a second argument for the observable:

    ngOnInit() {
      this.service.getPosts()
        .subscribe(response => {
          // render response...
        }, error => {
          alert ('An unexpected error occurred.');
          console.log(error);
        })
    }

#### Throwing application-specific errors

The example below shows how to throw application-specific errors inside a service, using rxJS and a custom AppError class:

    // post.service.ts (service)

    import { catchError, map } from 'rxjs/operators';
    import { throwError } from 'rxjs';
    import { AppError } from '../common/app-error';

    export class PostService {

      //...

      deletePost(id: number) {
        const deletedPostUrl = `${this.url}/${id}`;
        return this.http.delete(deletedPostUrl).pipe(
          map(data => {
            return data;
          }),
          catchError((error: Response) => {
            return throwError(new AppError(error));
          })
        )
      }

    }

    // app-error.ts (custom class)
    export class AppError {
      constructor (public originalError?: any) {}
    }

    // not-found-error.ts (custom class)
    import { AppError } from './app-error';
    export class NotFoundError extends AppError {}

    // in the component:
    deletePost(post) {
      this.service.deletePost(post.id)
        .subscribe(
          response => {
            // render response
        }, 
          (error: AppError) => {
            if (error instanceof NotFoundError){
              // handle not found error
            } else {
              // handle unexpected error
            }
          }
        )
    }

#### Global error handling

We can create a custom default handler for all unexpected errors, so that we don't have to handle this in each components. Angular has a class ErrorHandlar, that handles unexpected errors by default. This class can be replaced by our custom class, that will implement ErrorHandler.

    import { ErrorHandler } from '@angular/core';

    export class AppErrorHandler implements ErrorHandler {
        handleError(error) {
          // custom error handling happens here...
        }
    }

    // providers:

      providers: [
        { provide: ErrorHandler, useClass: AppErrorHandler }
      ],