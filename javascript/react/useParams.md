### ✅ useParams() – for URL path parameters
This hook is used to get variables from the path segments of your URL.

For example:
If your route is:

```bash
/post/list/:subCategoryId
```

and the actual URL is:

```bash
/post/list/abc123
```

then:

```tsx
const { subCategoryId } = useParams();
console.log(subCategoryId); // "abc123"
```

### ✅ useSearchParams() – for query parameters
This hook is for getting values from the query string (the part after ?).

For example:
If the URL is:

```bash
/post/list/abc123?page=3
```

then:

```tsx
const [searchParams] = useSearchParams();
const page = searchParams.get("page");
console.log(page); // "3"
```

🔁 Summary
| Feature        | `useParams()`                              | `useSearchParams()`                        |
|----------------|---------------------------------------------|---------------------------------------------|
| Reads from     | URL path (e.g. `/list/:id`)                 | Query string (e.g. `?page=1`)               |
| Returns        | An object with key-value pairs              | A `URLSearchParams` instance                |
| Example usage  | `const { id } = useParams();`               | `const [searchParams] = useSearchParams();` |
| Example value  | `id = "123"`                                | `searchParams.get("page") = "1"`            |
| Best for       | Identifying resources via route segments    | Pagination, filtering, search parameters    |

👀 Real-world Example
```tsx
<Route path="/post/list/:subCategoryId" element={<PostList />} />
```
With this URL:

```bash
/post/list/pizza?page=2
```
You get:

```tsx
const { subCategoryId } = useParams();         // "pizza"
const [searchParams] = useSearchParams();
const page = searchParams.get("page");         // "2"
```