Let's use a practical example with Express, a popular Node.js web framework.

Using require with CommonJS:
This is typically seen in older or server-side Node.js code.

javascript
Copy code
// server.js
const express = require('express'); // CommonJS require
const app = express();

app.get('/', (req, res) => {
  res.send('Hello, world!');
});

app.listen(3000, () => {
  console.log('Server is running on port 3000');
});
Using import with ES Modules (ESM):
In ESM, import is used. This is commonly seen in modern front-end frameworks or if "type": "module" is specified in package.json.

javascript
Copy code
// server.js
import express from 'express'; // ES Module import
const app = express();

app.get('/', (req, res) => {
  res.send('Hello, world!');
});

app.listen(3000, () => {
  console.log('Server is running on port 3000');
});
Key Differences
Syntax:

require is part of CommonJS (older Node.js standard).
import is part of ES Modules (native JavaScript modules for the browser and modern Node.js).
Module Scope:

CommonJS executes imports synchronously as the file runs.
ESM parses imports statically, allowing tools like tree-shaking (removing unused code) to work effectively in bundling.
Availability:

require works in Node.js without any additional setup.
import requires "type": "module" in package.json or the file extension .mjs in Node.js.
Using import is preferred in new projects, especially for front-end JavaScript and any projects that benefit from bundling and tree-shaking.

| Aspect            | `require` (CommonJS)                | `import` (ESM)                        |
|-------------------|-------------------------------------|---------------------------------------|
| **Module System** | CommonJS                            | ESM (ECMAScript Modules)              |
| **Where Used**    | Primarily Node.js                   | Modern JavaScript (Node.js and Browsers) |
| **Loading**       | Synchronous                         | Asynchronous                          |
| **Syntax**        | `const x = require('module');`      | `import { x } from 'module';`         |
| **Dynamic Imports** | Yes, you can require conditionally | No, must be at top-level, but `import()` can be used dynamically |
| **Default Exports** | `module.exports = something;`       | `export default something;`           |


When to Use Each
Use require (CommonJS) when you’re working in a Node.js environment that doesn’t support ESM or if you’re maintaining older codebases.
Use import (ESM) for modern JavaScript projects, especially if they run in the browser or a Node.js environment that supports ESM (Node.js version 12+ has ESM support).
Both methods are valid in Node.js (with recent versions), and you can convert between them using tools like Babel for older environments.