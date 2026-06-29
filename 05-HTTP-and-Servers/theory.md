# 05 — HTTP and Web Servers (raw, before frameworks)

Understand the raw `http` module so Express makes sense.

## 1. A minimal server
```js
const http = require("http");
const server = http.createServer((req, res) => {
  res.writeHead(200, { "Content-Type": "text/plain" });
  res.end("Hello from Node");
});
server.listen(3000, () => console.log("http://localhost:3000"));
```
`req` and `res` are streams. You **are** the server — there's no Tomcat. **SB parallel:** this is the level below `DispatcherServlet`.

## 2. Manual routing
```js
const server = http.createServer((req, res) => {
  if (req.url === "/" && req.method === "GET") {
    res.end("home");
  } else if (req.url === "/api/users" && req.method === "GET") {
    res.setHeader("Content-Type", "application/json");
    res.end(JSON.stringify([{ id: 1 }]));
  } else {
    res.writeHead(404); res.end("Not found");
  }
});
```

## 3. Read a request body (it streams in)
```js
http.createServer((req, res) => {
  let body = "";
  req.on("data", chunk => (body += chunk));
  req.on("end", () => {
    const data = JSON.parse(body || "{}");
    res.end(`Got ${data.name}`);
  });
});
```

## 4. Status codes you must know
| Code | Meaning |
|---|---|
| 200 | OK |
| 201 | Created |
| 204 | No content |
| 400 | Bad request |
| 401 | Unauthorized | 
| 403 | Forbidden |
| 404 | Not found |
| 409 | Conflict |
| 500 | Server error |

## 5. Headers, query, methods
```js
const url = new URL(req.url, `http://${req.headers.host}`);
url.pathname;                 // /search
url.searchParams.get("q");    // query param
req.method;                   // GET/POST/PUT/DELETE
req.headers.authorization;
```

## 6. Why frameworks exist
Manual routing/body parsing is tedious & error-prone. Express adds routing, middleware, body parsing, error handling. So we learn it next.

## SB parallels
| Servlet API | Node http |
|---|---|
| HttpServletRequest | req |
| HttpServletResponse | res |
| web.xml/mappings | manual if/else (then Express) |
| @ResponseStatus | res.writeHead(code) |

Next: `exercises.md`, then `06-Express`.
