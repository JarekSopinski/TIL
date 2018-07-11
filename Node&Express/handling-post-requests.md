## Handling POST requests

To handle POST requests in Express we can use Body Parser - a module already included in Express:

	const bodyParser = require('body-parser');

To parse POST data we can use urlencodedParser function by invoking it in our middleware. First we have to save it to a variable:

	const urlencodedParser = bodyParser.urlencoded({ extended: false })

Here's an example of a simple POST request. Middleware gives us access to 'req.body' object which we can log to the console. Than we render a submit success page that can render data from the request. 

	app.post('/contact', urlencodedParser, function(req, res){
	    console.log(req.body);
	    res.render('contact-success', {data: req.body})
	})

In this example a form that sends data can look like this (ejs is used to render data):

	<form id="contact-form" method="POST" action="/contact">
		<label for="who">Who do you want to contact?</label>
		<input type="text" name="who" value="<%= qs.person %>">
		<label for="department">Which department?</label>
		<input type="text" name="department" value="<%= qs.dept %>">
		<label for="email">Your email</label>
		<input type="email" name="email">
		<input type="submit" value="submit">
	</form>

As for the success page:

        <p>You contacted <%= data.who %> in the <%= data.department %> department.</p>
        <p>We will reply to you at <%= data.email %></p>
