## React Components

### Introduction

React Components can be created by extending React.Component class. They need to have at least one method, render(), that returns DOM's content. Inside render() method we can write JSX, that consists of html and JavaScript, run inside curly braces.

The result of render() method can be rendered using ReactDOM.render method, that takes two arguments: a component, written as a self-closing html tag, and a root (a place in a DOM where a component is rendered).

In the example below a div with "app" id is a root for "App" component:

    <div id="app"></div>

    <script type="text/babel">
        class App extends React.Component {
            render() {
                return (
                    <div className="app-content">
                        <h1>Hello world!</h1>
                        <p>{ Math.random() * 10 }</p> // an example of JS operation that can be included because of JSX
                    </div>
                )
            }
        }

        ReactDOM.render(<App />, document.getElementById('app'));
    </script>

### State and event handlers

A state object defines a state of its component. State's properties can be accessed and rendered inside JSX.

        class App extends React.Component {

            state = {
                name: 'John',
                age: 30
            }

            render() {
                return {
                    <div className="app-content">
                        <p>My name is {this.state.name} and I'm {this.state.age}</p>
                    </div>
                }
            }

        }

DOM elements inside render() method can have event handlers binded to them. We can declare this event handlers as methods of component class.

Note that if we want to access state inside event handlers, we have to bind its context to component's context. The easiest way is to declare them as arrow functions. We don't have to worry about bindind inside render() method, as React automaticly binds it to component's context.

Event handlers can change state using setState method (state should never be accessed directly). They can also access event object, if we pass it as an argument.

    handleClick = event => {
        console.log(event);
        this.setState({
            name: 'Adam'
        })
    }

    ...

    <button onClick={this.handleClick}>Click me to change name</button>

Note that inside JSX we should only have references to functions - they shouldn't be invoked. If we want to pass an argument, we can put an event handler inside anonymous function like this:

    <button onClick={ () => this.handleClick(arg) }>Click me to change name</button>

### Nesting components and passing props

Components can be nested and have parent-child relation. A parent can pass properties called "props" to its children Because of this components can be reusable - they can output different data, based on the props that are passed from their parents.

An example of parent component:

    class App extends Component {
        render(){
            return (
                <div>
                    <User name="Adam" age="30" />
                    <User name="John" age="25" />
                <div>
            )
        }
    }

An example of child component:

    class User extends Component {
        render(){
            const { name, age } = this.props; // here we use destructuring to save props' values to variables with the same names
            return (
                <div>
                    <div>Name: { name }</div>
                    <div>Age: { age }</div>
                <div>
            )
        }
    }

### Container components vs UI Components

There are two primary kinds of components.

Container components (class components) are responsible for handling data, state and lifecycle hooks. They are created as classes that extend React's Components class. They contain state object and pass its values to child components, nested within render() method.

UI components (functional components, stateless components) are simple views that don't contain state, only render data passed from parents through props. They're created as functions and simply return html (JSX) with dynamic props' data.

Of course, in functional components we can't access props as a property of a class. Instead, we have to pass it as a parameter.

Below is an example of User functional component that displays list of users passed from its parent class component.

    const User = props => {

            const { users } = props;

            const usersList = users.map(user => {
                return (
                    <li className="user" key={user.id}>
                        <div>Name: {user.name}</div>
                        <div>Age: {user.age}</div>
                    </li>
                )
            })

            return (
                <ul className="user-list">
                    { usersList }
                </ul>
            )

    }

    Note that props can be now destructurized directly in the parameter:

        const User = { users } => {

            const usersList = users.map(user => {
                ...
            })

            ...

    }

### Lifecycle

A component's lifecycle consists of three phases, and each phase has its own lifecycle methods (hooks):

1. Mounting phase, when component if first mountend into the DOM:

    1. constructor() - component gets an initial state

    2. getDerivedStateFromProps() - component updates its state based on props

    3. render()

    4. componentDidMount() - fires after component with initial state was mounted into the DOM. Usually data from API are loaded here 

2. Updating phase, when component is updated by passing new props or changing state:

    1. getDerivedStateFromProps() - fires if new props, that alert state, were  passed to a component

    2. shouldComponentUpdate() - a new state is compared to an old state

    3. render()

    4. getSnapshotBeforeUpdate() - here we have read acces to DOM before changes are    actually commited

    5. componentDidUpdate() - fires after component with new state was mounted into the DOM. Alerting state here will cause an infinite loop.

3. Unmounting phase, when component is unmounted from the DOM:

    1. componentWillUnmount()

[A nice interactive diagram that shows lifecycle can be found here.](http://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/)