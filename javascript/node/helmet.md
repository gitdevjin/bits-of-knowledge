`helmet` is a middleware for Express.js that helps secure your Node.js applications by setting various HTTP response headers. These headers protect your app from a wide range of common web vulnerabilities, including XSS (Cross-Site Scripting), clickjacking, and others.

### What Does `helmet` Do?
When you `require('helmet')` and use it in your Express application, it enables a set of security headers by default. Here's what it does:

#### 1. **X-Content-Type-Options**
- Sets the header: `X-Content-Type-Options: nosniff`
- Prevents the browser from interpreting files as a different MIME type than declared by the server.
- **Protection Against**: MIME type sniffing attacks.

#### 2. X-DNS-Prefetch-Control
- Sets the header: `X-DNS-Prefetch-Control: off`
- Disables DNS prefetching, which improves user privacy by not revealing requested domains unnecessarily.

#### 3. X-Frame-Options
- Sets the header: `X-Frame-Options: SAMEORIGIN`
- Prevents your site from being embedded in an iframe by other sites unless they are from the same origin.
- **Protection Against**: Clickjacking attacks.

#### 4. Strict-Transport-Security (HSTS)
- Sets the header: `Strict-Transport-Security: max-age=15552000; includeSubDomains`
- Forces the browser to access your site only over HTTPS for a specified period (default: 6 months).
- **Protection Against**: Protocol downgrade attacks.

#### 5. X-Permitted-Cross-Domain-Policies
- Sets the header: `X-Permitted-Cross-Domain-Policies: none`
- Disables Adobe Flash and Acrobat cross-domain requests.
- **Protection Against**: Cross-domain attacks targeting older technologies.

#### 6. Referrer-Policy
- Sets the header: `Referrer-Policy: no-referrer`
- Controls the amount of referrer information shared when navigating from your site to another.
- **Protection Against**: Leaking sensitive URL data.

#### 7. Content-Security-Policy (CSP)
- Sets a Content Security Policy header that restricts the sources from which content (e.g., scripts, images, styles) can be loaded.
- **Protection Against**: Cross-Site Scripting (XSS) and data injection attacks.
- **Note**: This header is not set by default in Helmet because it requires app-specific configuration.

### How to Use Helmet
#### Basic Usage
```javascript
const express = require('express');
const helmet = require('helmet');

const app = express();

// Use Helmet middleware
app.use(helmet());

app.get('/', (req, res) => {
  res.send('Hello, world!');
});

app.listen(3000, () => {
  console.log('Server running on port 3000');
});
```

#### Custom Configuration
If you want to enable or disable specific headers, you can configure Helmet:

```javascript
app.use(helmet({
  contentSecurityPolicy: false, // Disable CSP (for example)
}));
```

#### Adding Custom CSP Rules
You can configure a custom Content Security Policy like this:

``` javascript
const cspConfig = {
  directives: {
    defaultSrc: ["'self'"],
    scriptSrc: ["'self'", "example.com"],
    imgSrc: ["'self'", "images.com"],
  },
};

app.use(helmet.contentSecurityPolicy(cspConfig));
```

### Why Use Helmet?
1. **Ease of Use**: Helmet makes it simple to implement security best practices.
2. **Protection from Common Vulnerabilities**: Helps mitigate risks like XSS, clickjacking, and data injection.
3. **Customizable**: You can enable or disable specific features as needed for your app.