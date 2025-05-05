## How an HTTP response might look with different Content-Type headers
for various kinds of data Such as JSON, HTML, plain text, images, videos, etc.

### ✅ 1. JSON (application/json)
```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "message": "Hello, world!",
  "success": true
}
```

### javascript code in Node.js Server:
```javascript
app.get('/json', (req, res) => {
  res.type('application/json').status(200).send({
    message: 'Hello, world!',
    success: true
  });
});
```

### ✅ 2. HTML (text/html)
``` http
HTTP/1.1 200 OK
Content-Type: text/html

<!DOCTYPE html>
<html>
<head><title>Welcome</title></head>
<body>
  <h1>Hello, world!</h1>
</body>
</html>
```

### javascript code in Node.js Server:
```javscript
app.get('/html', (req, res) => {
  res.type('text/html').status(200).send(`
    <!DOCTYPE html>
    <html>
    <head><title>Welcome</title></head>
    <body>
      <h1>Hello, world!</h1>
    </body>
    </html>
  `);
});
```

### Or it serves with file
```javascript
// HTML response (serving a file)
app.get('/html', (req, res) => {
  res.status(200).sendFile(path.join(__dirname, 'index.html'), {
    headers: {
      'Content-Type': 'text/html'
    }
  });
});
```

### ✅ 3. Plain Text (text/plain)
```http
HTTP/1.1 200 OK
Content-Type: text/plain

Hello, world!
```

### javascript code in Node.js Server:
```
app.get('/text', (req, res) => {
  res.type('text/plain').status(200).send('Hello, world!');
});
```

### ✅ 4. Image (image/jpeg or image/png)
```http
HTTP/1.1 200 OK
Content-Type: image/jpeg

����JFIF���...binary data...
(The actual body would be raw binary bytes — not visible in plain text.)
```

### ✅ 5. CSS (text/css)
```http
HTTP/1.1 200 OK
Content-Type: text/css

body {
  background-color: #f0f0f0;
}
```

### javascript code in Node.js Server:
```js
app.get('/', (req, res) => {
  // Set the Content-Type header and send the CSS
  res.type('text/css').status(200).send(`
    body {
      background-color: #f0f0f0;
    }
  `);
});
```


### ✅ 6. JavaScript (application/javascript)
``` http
HTTP/1.1 200 OK
Content-Type: application/javascript

console.log('Hello, world!');
```

### ✅ 7. PDF (application/pdf)
```http
HTTP/1.1 200 OK
Content-Type: application/pdf

%PDF-1.4
%����
1 0 obj
<< /Type /Catalog ...
(Again, this would be raw binary PDF content.)
```

### ✅ 8. Video (video/mp4)
```http
HTTP/1.1 200 OK
Content-Type: video/mp4

...binary data stream of video...
```

In each case, the Content-Type tells the client how to interpret the response body — whether to render it as HTML, treat it as plain text, display an image, or download a file.