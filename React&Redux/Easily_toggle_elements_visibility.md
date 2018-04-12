## Easily toggle element's visibility in JSX

Let's say that we have a list and we want to toggle some items depending on same condition. We can do it easily like this:

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
