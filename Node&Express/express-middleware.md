## Using middleware with Express

Middleware is a code that runs between the request and the response.

We can set up a middleware through an Express use() method:

	app.use()

If we wouldn't specify route, the code inside this method would run on every request. Otherwise:

	app.use('/assets')

Now we now that the middleware is going to fire every time someone visits '/assets'.

The second argument passed to use() is of course a callback with request, response and, additionally, next parameter. 'Next' is responsible for going to another set of middleware after finishing this one.

	app.use('assets', function(req, res, next){...})

This simple middleware will log request url to the console - only upon visiting /assets - and then fire next():

	app.use('assets', function(req, res, next){
    		console.log(req.url);
    	next();
	})

Express comes with some built-in middleaware. For example, if we want to serve static files, we can use express.static():

	app.use('/assets', express.static('assets'));

In the above example static() is going to serve files from 'assets' directory upon visiting the correct route ('/assets').
