Description
-----------

Node-RQueue is a simple Redis based message queue for communicating between multiple platforms.

It uses a easy to implement message paradigm within Redis lists.

An example message:

	{
	  id: UUID,
	  payload: 'JSON DATA',
	  errors: ['HTTP 404'],
	  error_count: 1
	}

Messages are put onto the list with `rpush`, then the client listens for messages with the `blpop` command.

Usage
-----

queue-server.js:

	// Require the node-rqueue
	var client = require('./node-rqueue');

	// Create the Queue with a redis server listening on localhost:6379
	var user_queue = new client.Queue({
	  name: 'user',
	  host: 'localhost',
	  port: 6379
	});

	// Add a job to the queue.
	user_queue.write('Any JSON.strinify-able data goes in this parameter', function (error, id) {
	  // Handle errors
	  if (error) throw error;
	  // Second parameter is the message/job id.
	});

queue-worker.js:

	// Require the node-rqueue
	var client = require('./node-rqueue');

	// Create a worker for each queue.
	var worker = client.createWorker({
	  name: 'user',
	  host: 'localhost',
	  port: 6379
	});

	// Setup message event
	worker.on('data', function (job) {
	  // Process data
	  process(job.payload);

	  // Listen for next job
	  worker.next();
	});

	// Start listening.
	worker.start();
