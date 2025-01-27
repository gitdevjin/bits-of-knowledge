### What is `compression`?
`compression` is an Express middleware that compresses HTTP responses using gzip or brotli compression algorithms.

#### Why use it?
- Reduces the size of your server’s responses.
- Improves the speed of data transfer to clients.
- Saves bandwidth and improves user experience.

#### How to Use compression?
1. Install the Package

&nbsp;&nbsp;&nbsp;&nbsp; Use npm to add the middleware to your project:

```bash
npm install compression
```

2. Basic Usage

&nbsp;&nbsp;&nbsp;&nbsp; Import and add it to your Express app:

```javascript
const express = require('express');
const compression = require('compression');

const app = express();

// Use compression middleware
app.use(compression());

// Define a route
app.get('/', (req, res) => {
  res.send('Hello, compressed world!');
});

// Start the server
app.listen(3000, () => {
  console.log('Server is running on port 3000');
});
```

<br>

***

### What Happens When You Use It?
- When a client (e.g., a browser) requests data and supports compression (using the `Accept-Encoding` header), `compression` compresses the response.
The client decompresses the response, which results in faster loading times.

For example:

1. The client sends:
```makefile
GET / HTTP/1.1
Accept-Encoding: gzip, deflate
```

1. The server responds with a compressed response:
```css
HTTP/1.1 200 OK
Content-Encoding: gzip
```

<br>

***

### When to Use compression?
Use `compression` when:

- You want to speed up the delivery of text-based data like HTML, CSS, JavaScript, or JSON.
- You want to reduce bandwidth usage for your server.