## Custom streams

How to set up custom streams in Node.js?

First we have to get Node fs (file system) module:

	const fs = require('fs');

Now we can create a readable stream:

	const myReadStream = fs.createReadStream(__dirname + '/someText.txt')

The argument is a file that we want to read. Assuming that this file is in the same directory, we can use __dirname, which will point to it, and than we can concat path to the file itself.

This stream will slowly fill the buffer by sending it a small chunks of data. We can recognize the fact that one chunk of data was recived. To achive this, let's create an event listener:

	myReadStream.on('data', function(chunk) {
    		console.log('new chunk recived');
    		console.log(chunk)
	})

Now, by running the file in Node, we should see how data are being divided.

To see the actual text, we can just add coding argument ('utf8') to createReadStream() method.

Now let's create another type of stream - instead of a readable stream, this time it'll be a writeable stream. We have to use createWriteStream() method:

	const myWriteStream = fs.createWriteStream()

The argument is a path to which the data will be written:

	const myWriteStream = fs.createWriteStream(__dirname + '/writeMe.txt')

In this example we can use previously created readable stream. After the data has been read, it'll be written to the path that we specified:

	myReadStream.on('data', function(chunk) {
    		myWriteStream.write(chunk)
	})

Now the data should be sucessfully transfered, one bit at a time.

Pipes are used to recive data from read stream and pass it to a write stream. This process is automatic, so we don't have to manually listen for events. Therefore the code from last example is no longer needed.

Let's use a pipe() method on our read stream:

	myReadStream.pipe()

We need to pass an argument - our write stream:

	myReadStream.pipe(myWriteStream)

Now, if we run our app in Node, writeMe.txt will be created - as in previous example.

We can use pipe in our server to send data to a client. This time, we'll pass response (res) as an argument:

	const server = http.createServer(function(req, res) {

    		res.writeHead(200, {'Content-Type': 'text/plain'});

    		const myReadStream = fs.createReadStream(__dirname + '/someText.txt', 'utf8');
    		myReadStream.pipe(res)
    
	});


Now, if we run our app in Node and go the browser, we should see all data from someText.txt displayed in a browser.

