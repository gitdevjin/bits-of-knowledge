## 1. Basics of HTTP Requests
HTTP requests are used to send data from the client to the server. The data can be in various formats (e.g., JSON, FormData, etc.).

## 2. Different Ways to Send Data in HTTP Requests

### **JSON** (`application/json`)
- Used for sending structured data (objects or arrays).
- **Client**: `JSON.stringify()` is used to convert objects into a string.
- **Server**: The server typically uses `JSON.parse()` to reconstruct the object.
  
### **FormData** (`multipart/form-data`)
- Used when sending **files** or form data (including text).
- **Client**: Can send **text** and **files**. **No need to use `JSON.stringify()`** for simple text or file fields.
- **Server**: On the server, `multer` or similar middleware can handle parsing the form data and files.
  
### **URL Parameters** (`application/x-www-form-urlencoded`)
- Sent as part of the URL, commonly used for simple key-value pairs.
- **Client**: Typically appended to the URL as query parameters (e.g., `?key=value`).
- **Server**: Extracted directly from the URL (e.g., `req.query`).

### **Plain Text** (`text/plain`)
- Used for sending **raw text** data.
- **Client**: You can send plain text directly in the request body.
- **Server**: The server treats the body as raw text (no parsing is needed).
  
## 3. Sending Data: Non-Nested Objects vs. Nested Objects

### **Non-Nested (Simple) Objects**
- **Works without `JSON.stringify()`** when sending via `FormData`.
- `FormData` automatically converts simple text values (strings, numbers) into appropriate formats.
  
Example:

```js
const formData = new FormData();
formData.append("name", "hello world");  // No need to stringify
```
### Nested Objects (Objects within objects or arrays):

- Does not work directly. If you try to send a nested object via FormData without JSON.stringify(), it will be converted to [object Object].

- You must use JSON.stringify() to serialize the object so that it can be transmitted correctly.

```js
const user = { name: "hello world", preferences: { theme: "dark" } };
const formData = new FormData();
formData.append("user", JSON.stringify(user));  // Correct way
```

## 4. Why Use `JSON.stringify()` for Objects?
- JavaScript objects are in-memory structures and cannot be directly sent over HTTP.

- HTTP communication requires data to be in string or binary format (e.g., JSON strings or file data).

- `JSON.stringify()` converts an object into a string format, which can be safely transmitted over HTTP.

- On the server, the stringified object is parsed back into a usable object using JSON.parse().

## 5. Handling Files with `FormData`
- **FormData** is ideal for sending files (e.g., images) along with form data.

- **File handling**: The file is sent as binary data, and `FormData` handles this automatically, no need for `JSON.stringify()` for files.

Example:
```js
const formData = new FormData();
formData.append("file", fileInput.files[0]);  // Sends the file correctly
```

## 6. Server-Side Considerations
- **JSON Data**: Use `express.json()` middleware to parse JSON bodies. When receiving JSON, you will need to use JSON.parse() if you manually stringify the object.

Example:
```js
const user = JSON.parse(req.body.user);  // Parse stringified object from FormData
```
- **File Data**: Use middleware like multer to handle multipart form data (files) alongside text fields.

## 7. Summary of Key Concepts:
| Concept              | Details                                                                 |
|----------------------|-------------------------------------------------------------------------|
| **FormData**         | Used for sending key-value pairs and files in `multipart/form-data`.   |
| **`JSON.stringify()`** | Required to serialize nested JavaScript objects before sending.        |
| **Multer Middleware**| Express middleware used to handle `multipart/form-data`, especially for files. |
| **Non-Nested Objects** | Simple values (e.g., strings, numbers) can be sent directly via FormData. |
| **Nested Objects**   | Must be stringified using `JSON.stringify()` before appending to FormData. |
| **Server-side Parsing** | Use `JSON.parse()` on the server to convert stringified objects back to JS objects. |


## Key Takeaway:
- **Simple data** (like strings, numbers) can be sent directly in FormData without JSON.stringify().

- **Complex data** (like objects or arrays) requires stringification via JSON.stringify() to be sent properly over HTTP.

Use multer for handling files and `express.json()` for handling JSON data.


## 🔹 So how does a client send an HTTP request?

### ✅ 1. JSON (application/json)
Used for: APIs, especially RESTful services.
Format: Raw UTF-8 encoded text.

Example request body:

```json
{
  "username": "jin",
  "password": "123456"
}
```

Sent as:

```http
POST /login HTTP/1.1
Content-Type: application/json

{"username": "jin", "password": "123456"}
```

### ✅ 2. Form data (application/x-www-form-urlencoded)
Used for: HTML forms (`<form>` tags with default enctype).

Format: URL-encoded `key=value` pairs.

Example:

```http
POST /submit HTTP/1.1
Content-Type: application/x-www-form-urlencoded

username=jin&password=123456
```

This is When the client send form-data with http `form` tag
```html
<form action="/submit" method="POST">
  <input type="text" name="username" value="jin">
  <input type="password" name="password" value="123456">
  <input type="submit" value="Submit">
</form>
```



### ✅ 3. Multipart Form Data (multipart/form-data)
Used for: Uploading files via HTML forms.

Format: Binary chunks, boundaries between parts.

Example:

```http
POST /upload HTTP/1.1
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW

------WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="file"; filename="example.jpg"
Content-Type: image/jpeg

����JFIF���...binary bytes of the image...
------WebKitFormBoundary7MA4YWxkTrZu0gW--
```

This is when the client send it like the following
```html
<form action="/upload" method="POST" enctype="multipart/form-data">
  <input type="file" name="file">
  <input type="submit" value="Upload">
</form>
```

Or

```javascript
const formData = new FormData();
formData.append("username", "jin");
formData.append("password", "123456");
formData.append("file", fileInput.files[0]);

fetch('/upload', {
  method: 'POST',
  body: formData // Automatically sets content-type to multipart/form-data
});
```

In request, This will look like:
```http
POST /upload HTTP/1.1
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW

------WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="username"

jin
------WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="password"

123456
------WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="file"; filename="image.jpg"
Content-Type: image/jpeg

����JFIF���...binary bytes of the image...
------WebKitFormBoundary7MA4YWxkTrZu0gW--
```

### ✅ 4. Raw Binary (application/octet-stream)
Used for: Uploading raw files (e.g., images, videos) or custom binary data.

Format: Pure byte stream.

### ✅ 5. GraphQL (application/json)
Still uses JSON under the hood, but the payload usually contains a query string and optionally variables.

### ✅ 6. text Type
```http
POST /echo HTTP/1.1
Host: example.com
Content-Type: text/plain

Hello, this is just plain text.
```