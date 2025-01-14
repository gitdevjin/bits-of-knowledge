### Why Do You Need stoppable?
The stoppable library allows you to gracefully shut down an HTTP server, ensuring that ongoing requests are properly handled before the server stops. By default, when you stop an HTTP server in Node.js, it will immediately terminate without waiting for ongoing requests to complete. This can lead to unexpected behavior, like abruptly closing connections and leaving some operations incomplete.

stoppable extends the default server.close() method of the Node.js HTTP server to ensure that:

1. **Ongoing Requests Are Completed**: It waits for all in-progress requests to finish before shutting down the server.

2. **No New Requests Are Accepted**: The server stops accepting new connections as part of the shutdown process.

### How It Works

1. You wrap your server instance with stoppable.
2. When you invoke the stop() method on the wrapped server, it:
    - Closes the server to new connections.
    - Waits for any existing requests to finish.
    - Optionally times out requests if they take too long.


### Benefits of Using stoppable

1. **Ensures Reliability**: Prevents data loss or incomplete requests during server shutdown.
2. **Clean Shutdown**: Ideal for production applications where unhandled terminations can cause issues.
3. **Timeout Support**: You can specify a timeout for requests that take too long during shutdown.

By using stoppable, you can make your application more robust and production-ready.