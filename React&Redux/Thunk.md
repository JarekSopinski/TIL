## Thunk middleware

Thunk (redux-thunk) is a library used to perform async requests while using Redux. It runs as a middleware 'between' dispatching action and running the reducer. Thunk halts the dispatch, perfrom async request and, when request is completed, resumes dispatch and lets reducer update the store. The whole process looks like this, step by step:

1. Component dispatches an action with additional payload (i.e. ADD_POST);

2. Thunk halts the dispatch;

3. Async request is performed;

4. Async request is completed, Thunk resumes the dispatch;

5. Action is passed to the Reducer;

6. Reducer modifes the Store;

7. Store is updated and subscribing components recive new data.

### Setting up Thunk

Thunk can be setup in the root file (index.js). Besided Thunk itself, we have to use applyMiddleware available in Redux. This is a function that can be passed as a second argument to createStore and it can take a list of middlewares that we want to use:

    import { createStore, applyMiddleware } from 'redux';
    import thunk from 'redux-thunk';

    const store = createStore(rootReducer, applyMiddleware(thunk));

### Action creators

Without using Thunk, action creators would simply return action like this:

    export const createProject = project => {
        return { // return action
            type: 'CREATE_PROJECT',
            project: project
        }
    }

But with Thunk we can return a function, and inside that function: 1. run async code, 2. resume dispatch.

    export const createProject = project => {
        return (dispatch, getState) => {
            // run async code, like a call to database
            dispatch({ type: 'CREATE_PROJECT', project: project });
        }
    }

The above example makes use of currying and can be rewritten like this:

    export const createProject = project => (dispatch, getState) => {
        dispatch({ type: 'CREATE_PROJECT', project: project });
    }