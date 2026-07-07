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

---
## Extra exercises (mix of concept + build)

7. **(build)** Serve a static HTML file with the correct `Content-Type`.
8. **(build)** A tiny router: dispatch on `method + url`, with a 404 fallback.
9. **(build)** Make an outbound GET and parse JSON with native `fetch`.
10. Set a cookie header and send a 302 redirect.
11. Stream a large file as the response instead of buffering it in memory.
12. Add permissive CORS headers manually. Which header answers the preflight?
13. Handle graceful shutdown so in-flight requests finish before exit.

### Answers
```js
import http from "http"; import fs from "fs";
// 7
http.createServer((req,res)=>{res.setHeader("Content-Type","text/html");fs.createReadStream("index.html").pipe(res);}).listen(3000);
// 8
const routes={"GET /ping":(req,res)=>res.end("pong")};
const hdl=routes[`${req.method} ${req.url}`]; hdl?hdl(req,res):(res.writeHead(404),res.end());
// 9
const data=await (await fetch("https://api.github.com")).json();
// 10
res.writeHead(302,{"Set-Cookie":"sid=abc; HttpOnly","Location":"/home"}); res.end();
// 11
fs.createReadStream("big.mp4").pipe(res);   // backpressure-aware, flat memory
// 12  res.setHeader("Access-Control-Allow-Origin","*"); Access-Control-Allow-Methods answers the OPTIONS preflight.
// 13
process.on("SIGTERM",()=>server.close(()=>process.exit(0)));
```
