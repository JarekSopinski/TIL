## Misc React / Redux notes and tips

### Setting up Redux DevTools:

Repository with Redux DevTools Extension is availabe here:

	https://github.com/zalmoxisus/redux-devtools-extension

It can be installed from Chrome Store:

	https://chrome.google.com/webstore/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd

Then, inside our code (in Redux store), we need to set it up like this:

	const store = createStore(
	  reducer,
	  window.__REDUX_DEVTOOLS_EXTENSION__ && window.__REDUX_DEVTOOLS_EXTENSION__()
	)

### Easily toggle element's visibility in JSX

Let's say that we have a list and we want to toggle some items depending on some condition. We can do it easily like this:

	<ul>
		{ 2+2===4 && <li>I will be rendered!</li> }
		{ 2+2===5 && <li>I won't be rendered...</li> }
	</ul>

Another way with ternary operator:

	<ul>
		{this.state.foo ?
		<li>Foo</li>
		:
		<li>Bar</li>
		}
	</ul>