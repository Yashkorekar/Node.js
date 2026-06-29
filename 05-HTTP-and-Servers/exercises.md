# 05 — Exercises: HTTP and Servers

1. Start a server on port 4000 returning "pong" at `/ping`.
2. Return JSON `[{"id":1}]` at GET `/items`, else 404.
3. Read a POST body and echo back its `name` field.
4. Map: 201, 401, 409 → meanings.
5. Get query param `q` from `/search?q=node`.
6. Why move from raw http to Express?

---
## Answers
```js
// 1
require("http").createServer((req,res)=>{ if(req.url==="/ping")res.end("pong"); }).listen(4000);
// 2
if(req.url==="/items"){res.setHeader("Content-Type","application/json");res.end('[{"id":1}]');}else{res.writeHead(404);res.end();}
// 3
let b="";req.on("data",c=>b+=c);req.on("end",()=>res.end(JSON.parse(b).name));
// 4  201 Created, 401 Unauthorized, 409 Conflict
// 5
new URL(req.url,"http://x").searchParams.get("q");
// 6  routing, body parsing, middleware, errors handled for you -> less boilerplate.
```
