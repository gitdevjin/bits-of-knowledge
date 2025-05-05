🔹 What FormData does under the hood
When you create and send a FormData object, the browser:

Sets the request's Content-Type to multipart/form-data; boundary=... (including a unique boundary string).

Converts all the fields and files into a multipart body, where each part is separated by that boundary.

If you're uploading an image, it includes the image binary bytes inside that body.

✅ Example in client-side JavaScript
js
Copy
Edit
const formData = new FormData();
formData.append("username", "jin");
formData.append("avatar", fileInput.files[0]); // this is a File (e.g., image)

fetch("/upload", {
  method: "POST",
  body: formData // ⚠️ do NOT manually set Content-Type
});
👉 Important: Let the browser set Content-Type automatically.
If you manually set Content-Type, the browser won’t add the required boundary, and the server won’t parse the body correctly.

🔄 Example of what's sent
Here’s what the actual HTTP request body looks like:

pgsql
Copy
Edit
POST /upload HTTP/1.1
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryABC123

------WebKitFormBoundaryABC123
Content-Disposition: form-data; name="username"

jin
------WebKitFormBoundaryABC123
Content-Disposition: form-data; name="avatar"; filename="cat.jpg"
Content-Type: image/jpeg

(binary image bytes go here)
------WebKitFormBoundaryABC123--
✅ On the server side
In Node.js with express and multer:

```js
const multer = require('multer');
const upload = multer({ dest: 'uploads/' });

app.post('/upload', upload.single('avatar'), (req, res) => {
  console.log(req.body.username); // "jin"
  console.log(req.file); // file metadata (path, filename, mimetype, etc.)
});
```

So how does it work?
FormData uses multipart/form-data under the hood.

Images are sent as raw binary.

Each field is treated as a "part" with headers describing it (Content-Disposition, Content-Type, etc.).

The boundary is used to separate parts.