When using AWS S3 for image storage, you’re right to consider the scenario where a user uploads an image but then cancels the post creation. This introduces the need for cleanup to avoid orphaned files in S3. Here's how we can adapt the previous solution to use AWS S3 and handle this situation effectively.

### Strategy
1. Immediate Upload to S3: When the user adds an image via the rich text editor, upload it to S3 immediately and return the URL to embed in the editor.

2. Track Uploaded Images: Keep track of image URLs added to the editor content on the frontend.

3. Cleanup on Cancel: If the user cancels the post (e.g., navigates away or clears the editor without submitting), delete the uploaded images from S3 that aren't part of a saved post.

4. Post Submission: When the post is submitted, mark the images as "used" in the saved content, so they aren't deleted.

### Backend (Node.js with Express, Prisma, and AWS S3)
Setup
```bash
mkdir rich-text-s3-backend
cd rich-text-s3-backend
npm init -y
npm install express prisma @prisma/client aws-sdk multer cors dotenv
npx prisma init
```
.env
```plaintext
DATABASE_URL="postgresql://username:password@localhost:5432/rich_text_db?schema=public"
PORT=3001
AWS_ACCESS_KEY_ID=your_access_key
AWS_SECRET_ACCESS_KEY=your_secret_key
AWS_REGION=us-east-1
S3_BUCKET=your-bucket-name
```
prisma/schema.prisma
```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model Post {
  id        Int      @id @default(autoincrement())
  content   String   @db.Text
  createdAt DateTime @default(now())
}
```

Run migrations:
```bash
npx prisma migrate dev --name init
```

server.js
```javascript
require('dotenv').config();
const express = require('express');
const { PrismaClient } = require('@prisma/client');
const AWS = require('aws-sdk');
const multer = require('multer');
const cors = require('cors');

const app = express();
const prisma = new PrismaClient();
const PORT = process.env.PORT || 3001;

// AWS S3 Configuration
const s3 = new AWS.S3({
  accessKeyId: process.env.AWS_ACCESS_KEY_ID,
  secretAccessKey: process.env.AWS_SECRET_ACCESS_KEY,
  region: process.env.AWS_REGION,
});

const upload = multer({
  storage: multer.memoryStorage(), // Store in memory before uploading to S3
  limits: { fileSize: 5000000 }, // 5MB limit
  fileFilter: (req, file, cb) => {
    const filetypes = /jpeg|jpg|png/;
    const mimetype = filetypes.test(file.mimetype);
    if (mimetype) {
      return cb(null, true);
    }
    cb(new Error('Only images are allowed (jpeg, jpg, png)'));
  },
});

// Middleware
app.use(cors());
app.use(express.json());

// Upload image to S3
app.post('/api/upload-image', upload.single('image'), async (req, res) => {
  try {
    if (!req.file) {
      return res.status(400).json({ error: 'No image uploaded' });
    }

    const fileName = `${Date.now()}-${req.file.originalname}`;
    const params = {
      Bucket: process.env.S3_BUCKET,
      Key: fileName,
      Body: req.file.buffer,
      ContentType: req.file.mimetype,
      ACL: 'public-read', // Make image publicly accessible
    };

    const { Location } = await s3.upload(params).promise();
    res.json({ url: Location });
  } catch (error) {
    console.error('Error uploading to S3:', error);
    res.status(500).json({ error: 'Internal server error' });
  }
});

// Delete image from S3
app.post('/api/delete-image', async (req, res) => {
  const { url } = req.body;
  if (!url) {
    return res.status(400).json({ error: 'URL is required' });
  }

  try {
    const key = url.split('/').pop(); // Extract the file name from the URL
    const params = {
      Bucket: process.env.S3_BUCKET,
      Key: key,
    };

    await s3.deleteObject(params).promise();
    res.status(200).json({ message: 'Image deleted' });
  } catch (error) {
    console.error('Error deleting from S3:', error);
    res.status(500).json({ error: 'Internal server error' });
  }
});

// Create post
app.post('/api/posts', async (req, res) => {
  try {
    const { content } = req.body;
    if (!content) {
      return res.status(400).json({ error: 'Content is required' });
    }

    const post = await prisma.post.create({
      data: { content },
    });
    res.status(201).json(post);
  } catch (error) {
    console.error('Error creating post:', error);
    res.status(500).json({ error: 'Internal server error' });
  }
});

// Get all posts
app.get('/api/posts', async (req, res) => {
  try {
    const posts = await prisma.post.findMany({
      orderBy: { createdAt: 'desc' },
    });
    res.json(posts);
  } catch (error) {
    console.error('Error fetching posts:', error);
    res.status(500).json({ error: 'Internal server error' });
  }
});

app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```
### Frontend (React with TypeScript)
Setup
```bash
npm create vite@latest rich-text-app -- --template react-ts
cd rich-text-app
npm install react-quill axios
```

src/components/RichTextEditor.tsx
```tsx
import React, { useState, useMemo, useRef } from 'react';
import ReactQuill from 'react-quill';
import 'react-quill/dist/quill.snow.css';
import axios from 'axios';

const RichTextEditor: React.FC = () => {
  const [content, setContent] = useState<string>('');
  const [uploadedImages, setUploadedImages] = useState<string[]>([]); // Track uploaded image URLs
  const quillRef = useRef<ReactQuill>(null);

  // Custom image handler
  const imageHandler = async () => {
    const input = document.createElement('input');
    input.setAttribute('type', 'file');
    input.setAttribute('accept', 'image/*');
    input.click();

    input.onchange = async () => {
      const file = input.files?.[0];
      if (file) {
        const formData = new FormData();
        formData.append('image', file);

        try {
          const response = await axios.post('http://localhost:3001/api/upload-image', formData, {
            headers: { 'Content-Type': 'multipart/form-data' },
          });
          const imageUrl = response.data.url;

          // Insert image into editor
          const quill = quillRef.current?.getEditor();
          if (quill) {
            const range = quill.getSelection() || { index: 0 };
            quill.insertEmbed(range.index, 'image', imageUrl);
            setUploadedImages((prev) => [...prev, imageUrl]); // Track uploaded image
          }
        } catch (error) {
          console.error('Error uploading image:', error);
        }
      }
    };
  };

  const modules = useMemo(() => ({
    toolbar: {
      container: [
        [{ header: [1, 2, false] }],
        ['bold', 'italic', 'underline', 'strike'],
        [{ list: 'ordered' }, { list: 'bullet' }],
        ['link', 'image'],
        ['clean'],
      ],
      handlers: { image: imageHandler },
    },
  }), []);

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    try {
      const response = await axios.post('http://localhost:3001/api/posts', { content });
      console.log('Post saved:', response.data);
      setContent('');
      setUploadedImages([]); // Clear tracked images since they're now part of a saved post
    } catch (error) {
      console.error('Error saving post:', error);
    }
  };

  const handleCancel = async () => {
    // Delete all uploaded images that aren't part of a saved post
    for (const url of uploadedImages) {
      try {
        await axios.post('http://localhost:3001/api/delete-image', { url });
      } catch (error) {
        console.error('Error deleting image:', error);
      }
    }
    setContent('');
    setUploadedImages([]);
  };

  return (
    <div className="editor-container">
      <form onSubmit={handleSubmit}>
        <ReactQuill
          ref={quillRef}
          theme="snow"
          value={content}
          onChange={setContent}
          modules={modules}
          placeholder="Write something amazing..."
        />
        <div style={{ marginTop: '10px' }}>
          <button type="submit">Submit Post</button>
          <button type="button" onClick={handleCancel} style={{ marginLeft: '10px' }}>
            Cancel
          </button>
        </div>
      </form>
    </div>
  );
};

export default RichTextEditor;
```

### src/components/PostList.tsx
(Same as before)
```tsx
import React, { useEffect, useState } from 'react';
import axios from 'axios';

interface Post {
  id: number;
  content: string;
  createdAt: string;
}

const PostList: React.FC = () => {
  const [posts, setPosts] = useState<Post[]>([]);

  useEffect(() => {
    const fetchPosts = async () => {
      try {
        const response = await axios.get('http://localhost:3001/api/posts');
        setPosts(response.data);
      } catch (error) {
        console.error('Error fetching posts:', error);
      }
    };
    fetchPosts();
  }, []);

  return (
    <div>
      {posts.map((post) => (
        <div key={post.id} style={{ margin: '20px 0' }}>
          <div dangerouslySetInnerHTML={{ __html: post.content }} />
          <small>{new Date(post.createdAt).toLocaleString()}</small>
        </div>
      ))}
    </div>
  );
};

export default PostList;
```


### src/App.tsx

```tsx
import React from 'react';
import './App.css';
import RichTextEditor from './components/RichTextEditor';
import PostList from './components/PostList';

function App() {
  return (
    <div className="App">
      <h1>Rich Text Post Editor with S3 Images</h1>
      <RichTextEditor />
      <h2>Posts</h2>
      <PostList />
    </div>
  );
}

export default App;
```


## How It Works
### 1. Image Upload:
- When an image is added via the editor, it’s uploaded to S3 immediately via /api/upload-image.

- The returned S3 URL is embedded in the rich text content and added to the uploadedImages state.


### 2. Post Submission:
- On submit, the content (with embedded image URLs) is saved to the database.
- The `uploadedImages` array is cleared since the images are now part of a saved post.

### 3. Cancel Action:
- If the user clicks "Cancel," all images in uploadedImages are deleted from S3 via /api/delete-image.
- The editor content and tracked images are cleared.

### 4. Cleanup Considerations:
- This approach assumes cleanup happens when the user explicitly cancels. If the user closes the browser or navigates away without clicking "Cancel," orphaned images might remain in S3.

- To handle this, you could:
 - Add a periodic cleanup job on the backend to delete unused S3 objects (e.g., check for images not referenced in any Post content after a certain time).
 - Use a temporary S3 bucket or folder with a lifecycle rule to auto-delete objects after a set period (e.g., 24 hours) if they aren’t moved to a permanent location upon post submission.

## Enhancements
- *Security**: Sanitize the content with sanitize-html before saving or displaying to prevent XSS.

- **Robust Cleanup**: Implement a useEffect cleanup in the frontend to delete images when the component unmounts (e.g., on page navigation):
```tsx
useEffect(() => {
  return () => {
    handleCancel(); // Cleanup on unmount
  };
}, [uploadedImages]);
```

- **Image Management**: Store image metadata in the database if you need more control (e.g., to track usage or delete unused images later).

 - **Error Handling**: Add loading states and error messages for uploads and deletions.
Would you like me to implement any of these enhancements or adjust the cleanup strategy further?