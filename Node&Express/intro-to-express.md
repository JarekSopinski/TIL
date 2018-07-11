## Introduction to Express

Express (Express.js) is a popular framework that provides a nice set of features for node-based web applications.

By requiring Express and saving it to a variable we'll get access to its methods:

	const express = require('express');
	const app = express();

Now let's make it listen to a port:

	app.listen(3000);

A simple GET request can look like this:

	app.get('/', function(req, res){
    		res.send('This is a homepage')
	})

The first argument is a route and the second argument is a callback with request and response objects. Now, when you visit that route, server will send a response with a provided string. No need to specify content type - Express will recognize it by itself.

### Route params

Route params in an Express feature that lets us set the routes in a dynamic way. Lets imagine that we have a social app with a lot of user ids and we want to create routes based on each user's id. We can do it like this:

	app.get('/profile/:id', function(req, res){
    		res.send('You requested to see a profile with the id of ' + req.params.id)
	})

For example, if we now go to a route '/profile/:123', server will return a string ''You requested to see a profile with the id of 123'.

### Sending files, using template engines for rendering

To send files using Express we need to use sendFile() method, providing an absolute path to the file. For example:

	app.get('/', function(req, res){
    		res.sendFile(__dirname + '/index.html')
	})

	app.get('/contact', function(req, res){
    		res.sendFile(__dirname + '/contact.html')
	})

We can also set up a template engine - for example ejs:

	app.set('view engine', 'ejs');

Now, istead of using sendFile() method, we can use render() method:

	app.get('/', function(req, res){
    		res.render('index')
	})

This code will render index.ejs in a 'views' folder.
