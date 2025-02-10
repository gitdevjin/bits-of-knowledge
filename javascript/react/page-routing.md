## Page Routing

```javascript
export const onRequestGet: PagesFunction<Env> = async ({ request, env }) => {
    ...
}
```
This is a syntax for page routing, which is by default provided when deployed on cloudflare;
however, it is not provided on `aws ec2` or `ecs`
If you want to use this without cloudflare, you need a library such as `wrangler`


