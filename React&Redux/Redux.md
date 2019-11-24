## Redux

### Introduction

Redux is an implementaion of a flux design, that centralize state and data management. It's not a React-only tool, but can be used in other enviorments, i.e. Angular. A react-redux library is specific type of Redux meant to work with React.

Redux create a central data store, where it keeps all application's data. All components have direct access to the store, so they pass less data between themselves.

### Architecture

As it was mentioned above, Redux create a store, an application's data core. Components communicate with the store in two ways:

1. Getting data from the store

This is a simple process - components 'subscribe' to the store and when store is updated, new data are passed with the props to subscribed components.

2. Updating store's data

A data in the store can be updated based on user's action that is handled in a component. In other words, component have to inform store about a desired change and than store have to update its data according to that change. This process in more complex because components don't update store directly.

    1. Component - Dispatch Action 
    Component dispatches an action that can contain additional payload  (additional data, i.e. input's value). An action is an object with a type property, that describes it.

    2. Dispatch Action - Reducer
    Action is passed to the Reducer, a function responsible for managing store.

    3. Reducer - Store
    Reducer decides how to modify store based on the type property of this action and performs this modification.

    4. Store - Component
    After store was updated, subscribing components recive new data passed from the store inside props.

### Basic store

A most basic store can look like this:

    import { createStore } from 'redux';

    const initialState = {
        users: [],
        posts: []
    }

    // An action is passed to the reducer, so it can decide what to do with it:

    function myReducer(state = initialState, action){
        // Updating store logic happens here
    }

    // A reducer is passed to the store so that the store knows which reducer to interact with:

    const store = createStore(myReducer);

    // Action is an object that has a required type property (written in capital letters) and optional paylod (i.e. input's value):

    const addPostAction = { type: 'ADD_POST', post: 'Some post content' };

    // To dispatch the action we use store's dispatch method:
    
    store.dispatch(addPostAction);

Now let's expand myReducer so that it can actually modify store based on the type of action. A reducer returns updated state. Notice that we have to pass the whole previous state first (...state), otherwise we'd have lost properties other that the updated one (in this case - posts).

    function myReducer(state = initialState, action){
        switch (action.type){
            case 'ADD_POST':
                return {
                    ...state,
                    posts: [...state.posts, action.post]
                }
            break;
        }
    }

### Subscribing to store's changes

To listen for store's changes we have to use store's subscribe() method, that expects a function as an argument. Current state is accesible with store's getState() method.

    store.subscribe(() => {
        console.log(store.getState())
    })

### Integration with React

Inside React a store is usually created in the root component (index.js). To create a store, we need to import createStore from Redux. Than, to integrate application with Redux, we need to put the main component into Provider, that can be imported from react-redux library. A Provider serves as a 'glue' between React and Redux. We have to pass it a store, created with createStore method. As was mentioned above, this method expects a reducer function - in this example it's called 'rootReducer'.

    import { createStore } from 'redux';
    import { Provider } from 'react-redux';
    import rootReducer from './reducers/rootReducer';

    const store = createStore(rootReducer);
    
    ReactDOM.render(
        <Provider store={store}>
            <App />
        </Provider>, 
        document.getElementById('root')
        );

### Connecting component to the store

A connection from store to the component is provided by connect() function. This is a higher order component that can be imported from react-redux library.

#### Mapping state to props (store -> component connection) example:

    import { connect } from 'react-redux';

    class Home extends Component {
        ...
    }

    const mapStateToProps = state => {
        return {
            posts: state.posts
        }
    }

    export default connect(mapStateToProps)(Home);

#### Mapping dispatch to props (component -> reducer connection) example:

    // an event handler calls a deletePost() function passed through props:
    handleDeleteClick = () => {
        this.props.deletePost(this.props.post.id);
    }

    class Home extends Component {
        ...
        <button onClick={this.handleDeleteClick}>
            Delete post
        </button>
        ...
    }

    // deletePost() dispatches an action to the reducer:
    const mapDispatchToProps = dispatch => {
        return {
            deletePost: id => {
                dispatch({ type: 'DELETE_POST', id: id })
            }
        }
    }

    export default connect(mapStateToProps, mapDispatchToProps)(Post);

Than, after the above action was passed to the reducer, it can modify state based on this action.

In the reducers file:

    const rootReducer = (state = initState, action) => {
        switch (action.type) {
            case 'DELETE_POST':
                let newPosts = state.posts.filter(post => {
                    return action.id !== post.id
                });
                return {
                    ...state,
                    posts: newPosts
                }
            default:
                return {
                    ...state
                }
        }
    }

### Action creators

Instead of dispatching an action like this:

    const mapDispatchToProps = dispatch => {
        return {
            deletePost: id => {
                dispatch({ type: 'DELETE_POST', id: id })
            }
        }
    }

...it's better to create reusable action creators in a separate file. In this example such file can be called postActions.js. We can than import creators to the components.

Now, to modify previous example:

In postActions.js:

    export const deletePost = (id) => {
        return {
            type: 'DELETE_POST',
            id // same as id: id
        }
    }

And in the component:

    import { deletePost } from '../actions/postActions';

    ...

    const mapDispatchToProps = dispatch => {
    return {
        deletePost: id => {
            dispatch(deletePost(id))
        }
    }
}