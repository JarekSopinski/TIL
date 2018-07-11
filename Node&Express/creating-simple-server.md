## Creating simple server with Node.js

To create a server we need one of Node's core modules - a http module.

	const http = require('http');

Then we can use its createServer method to create a server and store it in a variable:

	const server = http.createServer();

This method accepts a callback with two parameters: request and response objects:

	const server = http.createServer( function(req, res) {...})

Now we should add response headers to the response object. Below first parameter is a status and a second paramter is content type (object):

	const server = http.createServer( function(req, res) {
		res.writeHead(200, {'Content-Type': 'text/plain'})

	})

Now let's add a method responsible for ending response and sending it to a browser:

	const server = http.createServer( function(req, res) {
		res.writeHead(200, {'Content-Type': 'text/plain'})
		res.end('Hello World')

	})

Finally, we have to specify a port for our server (as a number). Also, we have to pass IP adress (hostname), as a string:

	server.listen(3000, '123.456.7.89')

Of course, it's always a good practice to save these values to variables:

	const port = 3000;
	const hostname = '123.456.7.89';
	server.listen(port, hostname);

The server will start running after we run this file in Node. We can test it by opening our browser and typing hostname:port (for ex.: 123.456.7.89:3000). If the server runs correctly, we should see "Hello World" as a plain text.
