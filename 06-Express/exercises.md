# 06 — Exercises: Express

1. Create an app with GET `/` returning "ok" and listening on 3000.
2. Add GET `/hello/:name` returning `Hello <name>`.
3. Write a logger middleware applied globally.
4. Add a POST `/echo` that returns the JSON body back.
5. Split user routes into a Router mounted at `/users`.
6. Add an error-handling middleware returning JSON `{error}`.
7. Difference: `req.params` vs `req.query` vs `req.body`.

---
## Answers
```js
import express from "express"; const app=express(); app.use(express.json());
// 1
app.get("/",(req,res)=>res.send("ok"));
// 2
app.get("/hello/:name",(req,res)=>res.send(`Hello ${req.params.name}`));
// 3
app.use((req,res,next)=>{console.log(req.method,req.url);next();});
// 4
app.post("/echo",(req,res)=>res.json(req.body));
// 5  routes/users.js exports Router; app.use("/users", users);
// 6
app.use((err,req,res,next)=>res.status(500).json({error:err.message}));
app.listen(3000);
// 7  params=path vars, query=?key=val, body=JSON payload
```

---
## Extra exercises (mix of concept + build)

8. **(build)** Serve static files from a `/public` folder.
9. **(build)** An async route + wrapper that forwards thrown errors to the error middleware.
10. Add a 404 catch-all AFTER all routes — why must it be last?
11. Order matters: what breaks if `express.json()` is registered after a POST route?
12. **(build)** Use `router.param("id", ...)` to load a resource once for any `/:id` route.
13. Chain multiple middleware on one route (auth → validate → handler).
14. Return proper codes: 201 on create, 204 on delete (no body).

### Answers
```js
import express from "express"; const app=express(); app.use(express.json());
// 8
app.use(express.static("public"));
// 9
const wrap=fn=>(req,res,next)=>Promise.resolve(fn(req,res,next)).catch(next);
app.get("/u",wrap(async(req,res)=>{const u=await find();res.json(u);}));
// 10  Express matches top-down; a catch-all placed before real routes would shadow them.
app.use((req,res)=>res.status(404).json({error:"not found"}));
// 11  req.body is undefined for that route — the body parser never ran before the handler.
// 12
router.param("id",async(req,res,next,id)=>{req.item=await find(id);next();});
// 13
app.post("/p", auth, validate, (req,res)=>res.status(201).json(req.body));
// 14  res.status(201).json(created);  ...  res.status(204).end();
```
