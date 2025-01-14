```javascript

process.on('uncaughtException', (err, origin) => {
    logger.fatal({ err, origin }, 'uncaughtException');
    throw err;
})

process.on('unhandledRejection', (reason, promise) => {
    logger.fatal({ reason, promise}, 'unhandledRejection');
    throw reason;
})
```
### What These Events Do
#### uncaughtException:

Triggered when an exception is thrown and not caught anywhere in the code.
This is useful for logging critical errors before the application crashes.

#### unhandledRejection:

Triggered when a Promise is rejected and no .catch handler is attached to handle the rejection.
This is used to log errors from asynchronous operations before they cause issues.