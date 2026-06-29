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
