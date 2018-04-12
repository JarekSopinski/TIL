## Setting up Redux DevTools:

Repository with Redux DevTools Extension is availabe here:

	https://github.com/zalmoxisus/redux-devtools-extension

It can be installed from Chrome Store:

	https://chrome.google.com/webstore/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd

Then, inside our code (in Redux store), we need to set it up like this:

	const store = createStore(
	  reducer,
	  window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()
	)
