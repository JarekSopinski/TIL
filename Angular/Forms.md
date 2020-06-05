## Angular Forms

### Form Control and Form Group

For each input in a form, a new instance of FormControl class is created. It can check input's state, represented by values such as:

- value
- touched
- dirty
- pristine
- valid
- errors

FormGroup class repesents a group of controls in a form.

### Template Driven Forms vs Reactive Forms

There are two ways to create control objects:
- using directives - these are Template Driven Forms;
- explicitly creating forms with new instances of FormControl and FormGroup - these are Reactive Forms.

Template Driven Forms are better for simple forms and easy validation. They are easier to created. Less code, but less control.
Reactive Forms give more control over validation logic and are better suited for complex forms. They allow us to write unit tests.

### Template Driven Forms (TDF)

For each input we use ngModel directive. It's required for the input to have name attribute. It allows us to create a new instance of FormControl class for this input.

    <input ngModel name="firstName" id="firstName" type="text" class="form-control">

#### TDF - ngModel directive and validation

To perform a validation, we can use the required attribute from HTML5. We can toggle validation alerts' visibilty using properties of ngModel (in the example below it's saved to firstName variable). In this example alert will be displayed only if valid is false, after input was touched.

        <input required ngModel name="firstName" #firstName="ngModel" (change)="log(firstName)" id="firstName" type="text" class="form-control">

        <div class="alert alert-danger" *ngIf="firstName.touched && !firstName.valid">First Name is required.</div>


Using other properties of ngModel, we can perform more complex validation.

        <input required minlength="3" maxlength="10" pattern="banana" ngModel name="firstName" #firstName="ngModel" (change)="log(firstName)" id="firstName" type="text" class="form-control">

        <div class="alert alert-danger" *ngIf="firstName.touched && !firstName.valid">
            <div *ngIf="firstName.errors.required">First name is required</div>
            <div *ngIf="firstName.errors.minlength">First name should be minimum {{ firstName.errors.minlength.requiredLength }} chars.</div>
            <div *ngIf="firstName.errors.pattern">First name doesn't match the pattern.</div>
        </div>

#### TDF - ngForm directive and input's value

ngForm directive is automaticly added to each form. We can use properties of this directive, including value - an object that represents value of the whole form. We can also hook to a submit event, using output property ngSubmit - an event fired upon submitting the form. In the example below, on the submit we display form's value (#f variable represents ngForm).

    <form #f="ngForm" (ngSubmit)="submit(f)">
        ...
    </form>

    export class ContactFormComponent {
        submit(f){ console.log(f.value); }
    }

#### TDF - ngModelGroup - dividing TDF into smaller groups

Complex forms can be dividied into smaller groups using ngModelGroup directive. This divisin will be represented in the structure of f.value object.

    <form #f="ngForm" (ngSubmit)="submit(f)">
        <div ngModelGroup="contact" #contact="ngModelGroup">
        </div>
        <div ngModelGroup="billing" #billing="ngModelGroup">
        </div>
    </form>

#### TDF - checkboxes

An example of adding isSubscribed checkbox:

    <div class="checkbox">
        <label>
            <input type="checkbox" ngModel name="isSubscribed"> Subscribe to mailing list
        </label>
    </div>

#### TDF - select and radio buttons

An example of adding select with dynamic options' value:

    <div class="form-group">
        <label for="contactMethod">Contact Method</label>
        <select 
            ngModel
            name="contactMethod" 
            id="contactMethod" 
            class="form-control">
            <option 
                *ngFor="let method of contactMethods" 
                [value]="method.id">
                {{ method.name }}
            </option>
        </select>
    </div>

    export class ContactFormComponent {

      contactMethods = [
        { id: 1, name: 'Email' },
        { id: 2, name: 'Phone' }
      ]

    }

To allow user to select multiple values, we have to add 'multiple' attribute to the select.

In simillar way we can create radio buttons group:

    <div *ngFor="let method of contactMethods" class="radio">
        <label>
            <input ngModel type="radio" name="contactMethod" [value]="method.id">
            {{method.name}}
        </label>
    </div>


### Reactive Forms (RF)

In Reactive Form we create control objects explicitly in the code.
We start by creating new instance of FormGroup(). It has one required parameter - controls. Controls is an object built from keys and values, the values being instances of AbstractControl class. AbstractControl is a base class for FormControl and FormGroup. FromControl and FormGroup inherit from AbstractControl. So we can use either FormControl for single fields or FormGroup for groups of multiple fields.

Note that ReactiveFormsModule has to be imported into component's parent module.

In the example below, 'form' is a name of FormGroup's new instance that we create in our component and bind to form in the template file. For each input filed we use formControlName directive set to the key of FormControl object.

    import { FormGroup, FormControl } from '@angular/forms';

    export class SignupFormComponent {

      form = new FormGroup({
        username: new FormControl(),
        password: new FormControl()
      });

    }

    <form [formGroup]="form">
        <div class="form-group">
            <label for="username">Username</label>
            <input 
                formControlName="username"
                id="username" 
                type="text" 
                class="form-control">
        </div>
        <div class="form-group">
            <label for="password">Password</label>
            <input 
                formControlName="password"
                id="password" 
                type="text" 
                class="form-control">
        </div>
        <button class="btn btn-primary" type="submit">Sign Up</button>
    </form>

#### RF - validation

In Reactive Forms we don't use HTML5 attributes for validation. We assign validator when creating FormControl objects. Upon creating these objects, we can assign one or more validators. Validator is a second optional parameter of FormControl. It can be a validator function or an array of validator functions. Validator functions are static methods of Validators class, that we have to import from @angular/forms.

I.e. we can use:

- Validators.required,
- Validators.minLength,
- Validators.maxLength,
- Validators.pattern,
- Validators.email

Note that these methods are static. We don't call them, but simply pass references to them.
Also note that in the example below we use empty string to go over first argument.

    export class SignupFormComponent {

      form = new FormGroup({
        username: new FormControl('', Validators.required),
        password: new FormControl('', Validators.required)
      });

    }

Now we can display validation messages in the template by accessing FormGroup's properties with get() method:

     <input 
         formControlName="username"
         id="username" 
         type="text" 
         class="form-control">
         <div *ngIf="form.get('username').touched && form.get('username').invalid" class="alert alert-danger">Username is required.</div>

This can be simplified by defining a getter for 'username':

    get username() {
          return this.form.get('username');
    }

    <div *ngIf="username.touched && username.invalid" class="alert alert-danger">Username is required.</div>


Multiple validator functions can be added in an array. In the example below we add required() and minLenght():

    form = new FormGroup({
      username: new FormControl('', [
        Validators.required,
        Validators.minLength(3)
      ]),
      password: new FormControl('', Validators.required)
    });

#### RF - custom validator function

We can create custom validator functions. Validator function has to take AbstractControl as a parameter and return ValidationErrors or null. For example:

    import { AbstractControl, ValidationErrors } from '@angular/forms';

    export class UsernameValidators {

        static cannotContainSpace(control: AbstractControl) : ValidationErrors | null {
            if ((control.value as string).indexOf(' ') >= 0)
                return { cannotContainSpace: true };

                return null;
        }

    }

Such validator can now be added to FormControl and used in a form template.

### RF - asynchronous validators

If we want to create a validator function that return the result of an asynchrous operation, we have to use a different signature - AsyncValidatorFn.

Asynchronous validator functions can be added as a third parameter of FormControl.

Just like "normal" validators, async validator functions take AbstractControl as parameter. But the return type is a Promise of ValidationErrors | null, or an Observable of ValidationErrors | null.

In the example below we simulate a call to the server (i.e. to check if name is unique in database) with a timeout. We return a new Promise object. Then we use Promise's parameters - resolve and reject functions - to either return some value to the consumer of this Promise or return an error.

    static shouldBeUnique(control: AbstractControl) : Promise<ValidationErrors | null> {
        return new Promise((resolve, reject) => {
            setTimeout(() => {
                if (control.value === 'jarek')
                    resolve ({ shouldBeUnique: true });
                else 
                    resolve(null);
            }, 2000);
        });
    }

If we want to display a loader while async action is in progress, we can refer 'pending' property from our async validator. 'Pending' returns true if the action hasn't finished yet.

    <div *ngIf="username.pending">Cheking for uniqueness...</div>

### RF - validation on form submit

To validate on submit, we can bind ngSubmit event to our custom method (in the example below - login()):

    <form [formGroup]="form" (ngSubmit)="login()">
        <div *ngIf="form.errors" class="alert alert-danger">
            The username or password is invalid.
        </div>
        //..
    </form>

Now, let's imagine that we have a class called AuthService with login method, that checks if login is correct after connecting to the server. If this returns false, we can display validation errors:

    login() {
        let isValid = AuthService.login(this.form.value);
        if (!isValid) {
          this.form.setErrors({
            invalidLogin: true
          })
        }
    }

### RF - nested form groups

We can nest FormGroup objects with itself:

    form = new FormGroup({
      account: new FormGroup({
        username: new FormControl(''),
        password: new FormControl('')
      })
    });

The above structure has to mirrored in the template:

    <div formGroupName='account'>
        <div class="form-group">
            // username
        </div>
        <div class="form-group">
            // password
        </div>
    </div>

### RF - FormArray

Sometimes we need to work with an array of objects in a form.
When dealing with an array of objects, instead of FormControl we use a FormArray class.
Its first parameter expects an AbstractControl array - a container for all objects.

In the example below, user inputs an array of objects - topics:

    export class TopicsFormComponent {

      form = new FormGroup({
        topics: new FormArray([])
      });

      addTopic(topic: HTMLInputElement) {
        this.topics.push(new FormControl(topic.value));
        topic.value = '';
      }

      removeTopic(topic: FormControl) {
        let index = this.topics.controls.indexOf(topic);
        this.topics.removeAt(index);
      }

      get topics(){
        return this.form.get('topics') as FormArray;
      }

    }

    <form>
        <input type="text" class="form-control"
        (keyup.enter)="addTopic(topic)" #topic
        >
        <ul class="list-group">
            <li *ngFor="let topic of topics.controls"
                (click)="removeTopic(topic)"
                class="list-group-item">
                {{ topic.value }}
            </li>
        </ul>
    </form>

### RF - FormBuilder

FormBuilder class serves as a 'shortcut' to easily create complex form.
Let's say that we want to build a form with a 'normal' approach:

    form = new FormGroup({
      name: new FormControl('', Validators.required),
      contact: new FormGroup({
        email: new FormControl(),
        phone: new FormControl()
      }),
      topics: new FormArray([])
    });

We can get the same result by using FormBuilder like this:

    constructor(fb: FormBuilder){
      this.form = fb.group({
        name: ['', Validators.required], // we pass FormControl args into the array
        contact: fb.group({
          email: [],
          phone: []
        }),
        topics: fb.array([])
      })
    }

Notice that arrays serves for passing FormControl arguments.