# What does multer do when it gets the following request?

```http
POST /upload HTTP/1.1
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW

------WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="image"; filename="example.jpg"
Content-Type: image/jpeg

����JFIF���...binary bytes of the image...
------WebKitFormBoundary7MA4YWxkTrZu0gW--
...
``` 


## 🛠 What Multer Does (Step by Step):
### 1. Reads the Request Body Stream:
Multer intercepts the incoming HTTP request and reads the raw stream, detecting the `Content-Type: multipart/form-data` header.

### 2. Identifies the Boundary:
It uses the `boundary` from the header (`----WebKitFormBoundary7MA4YWxkTrZu0gW`) to split the body into individual parts.

### 3. Parses Each Part:
It separates each part of the multipart body:

- `Content-Disposition: form-data; name="image"; filename="example.jpg"`

- `Content-Type: image/jpeg`

- Actual binary image data

### 4. Handles File Parts:
If a part has a `filename`, it's considered a file:

- Multer saves the file to disk or memory (based on your config).

- It populates req.file (or req.files for multiple) with metadata:

```js
{
  fieldname: 'image',
  originalname: 'example.jpg',
  encoding: '7bit',
  mimetype: 'image/jpeg',
  buffer: <Buffer>,        // only if using memoryStorage
  path: '/uploads/example.jpg', // if using diskStorage
  size: 123456
}
```

### 5. Handles Text Fields:
For non-file fields (like name="username"), it adds them to req.body.

#### 💡Example Code:
```js
const multer = require('multer');
const upload = multer({ dest: 'uploads/' });

app.post('/upload', upload.single('image'), (req, res) => {
  console.log(req.file); // File info
  console.log(req.body); // Other text fields
});
```

🧠 Summary Table:
|Part Type |	What Multer Does |	Where It Goes |
|----------------------|----------------------------------------|---------------------------------|
|File (has filename) |	Saves to disk or memory	|req.file or req.files|
|Text Field |	Parses as string, adds to request body |	req.body.fieldName |






# What if the client send name="image", name="video", like severals

Great question! If the client sends multiple files using different name attributes like "image", "video", etc., Multer can handle that — but you’ll need to set it up accordingly on the server.

✅ When the client sends:
```http
Content-Disposition: form-data; name="image"; filename="img.jpg"
Content-Disposition: form-data; name="video"; filename="vid.mp4"
```
or via JavaScript:

``` js
const formData = new FormData();
formData.append("image", imageFile);
formData.append("video", videoFile);

fetch('/upload', {
  method: 'POST',
  body: formData
});
```

✅ You handle this on the server like:
### 1. Use .fields() with multer
You define expected field names and how many files per field.

```js
const multer = require('multer');
const upload = multer({ dest: 'uploads/' });

app.post(
  '/upload',
  upload.fields([
    { name: 'image', maxCount: 1 },
    { name: 'video', maxCount: 1 }
  ]),
  (req, res) => {
    console.log(req.files.image); // [{ file info }]
    console.log(req.files.video); // [{ file info }]
    console.log(req.body);        // text fields
    res.send('Files uploaded!');
  }
);
```
`req.files` will be an object with keys matching the field names.

✅ Output Example of req.files:
```js
{
  image: [
    {
      fieldname: 'image',
      originalname: 'img.jpg',
      encoding: '7bit',
      mimetype: 'image/jpeg',
      destination: 'uploads/',
      filename: 'abc123.jpg',
      path: 'uploads/abc123.jpg',
      size: 23456
    }
  ],
  video: [
    {
      fieldname: 'video',
      originalname: 'vid.mp4',
      mimetype: 'video/mp4',
      path: 'uploads/xyz789.mp4',
      size: 1048576
    }
  ]
}
```
