### Three-Way Knock in CORS
1.&nbsp; **First Knock (Preflight Request)**:
The browser sends a preflight request using the HTTP OPTIONS method.
This is automatically done by the browser when:

- The request uses a method other than `GET` or `POST` (e.g., `PUT`, `DELETE`).
- The request includes custom headers like `Authorization` or `Content-Type: application/json`.
- The request requires credentials (e.g., cookies or HTTP authentication).

#### Example Preflight Request:

```http
OPTIONS /api/resource HTTP/1.1
Origin: http://client.com
Access-Control-Request-Method: POST
Access-Control-Request-Headers: Content-Type
```

- **Key Purpose**: The browser is asking, "Can I safely send this request to your server from this origin (`http://client.com`) with the specified method and headers?"

2.&nbsp; **Server Response to Preflight**:
The server, if configured with the `cors` middleware, responds to the preflight request with appropriate CORS headers. This tells the browser whether the actual request is allowed.

#### Example Response:

```http
HTTP/1.1 204 No Content
Access-Control-Allow-Origin: http://client.com
Access-Control-Allow-Methods: POST
Access-Control-Allow-Headers: Content-Type
Access-Control-Allow-Credentials: true
Access-Control-Max-Age: 86400
```

- **Key Headers Explained**:
    - `Access-Control-Allow-Origin`: Specifies allowed origin(s).
    - `Access-Control-Allow-Methods`: Specifies allowed HTTP methods.
    - `Access-Control-Allow-Headers`: Specifies allowed headers.
    - `Access-Control-Allow-Credentials`: Indicates if credentials are allowed.
    - `Access-Control-Max-Age`: Tells the browser how long to cache the preflight result.

3.&nbsp; **Second Knock (Actual Request)**:
Once the browser receives a successful response to the preflight request, it sends the actual request.

#### Example Actual Request:

```http
POST /api/resource HTTP/1.1
Origin: http://client.com
Content-Type: application/json
Cookie: session_id=abcd1234
```
   
- The `Origin` header is included in the request to indicate where the request is coming from.


4.&nbsp; **Server Response to Actual Request**:
The server processes the actual request and includes the appropriate **CORS headers** in its response.

#### Example Response:

```http
HTTP/1.1 200 OK
Access-Control-Allow-Origin: http://client.com
Access-Control-Allow-Credentials: true
Content-Type: application/json
```


- The browser ensures that the `Access-Control-Allow-Origin` value matches the `Origin` of the request. If it doesn't, the browser blocks the response.

### Important Notes
 - **Preflight Requests Are Automatic**: The browser handles preflight requests without developer intervention.

 - **Caching**: The `Access-Control-Max-Age` header helps reduce preflight requests by caching the result for the specified duration.

 - **Single Knock for Simple Requests**: For "simple" requests (e.g., `GET` or `POST` with no custom headers), no preflight request is sent, and the process skips directly to the actual request.

### Summary Flow

1. **Client**: Sends `OPTIONS` preflight request to check server's CORS policy.

2. **Server**: Responds with `Access-Control-*` headers indicating whether the actual request is allowed.

3. **Client**: If allowed, sends the actual request.

4. **Server**: Processes the actual request and responds with data and appropriate CORS headers.

This ensures secure communication across different origins while adhering to the browser's CORS policy.