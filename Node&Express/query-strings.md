## Query strings

Query string is an additional data added to the http request in the form of a name and a value. 

In Express, to access a query string, we can use 'query' property of the request object:

	app.get('/contact', function(req, res){
    		console.log(req.query);
    	res.render('contact')
	})

Now, for example, if we type a path '/contact?dept=marketing&person=joe', we'll see an object in our console:

	{ dept: 'marketing', person: 'joe' }

Therefore it's very easy for us to access the query string's data.

If we want to render query's data, we can do it like this:

	app.get('/contact', function(req, res){
    		res.render('contact', {qs: req.query})
	})
