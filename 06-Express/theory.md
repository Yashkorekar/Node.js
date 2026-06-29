# 06 — Express Framework

Express is the de-facto Node web framework. Think of it as your Spring MVC.

## 1. Setup
```bash
npm init -y && npm install express
```
```js
// app.js
import express from "express";        // or const express = require("express")
const app = express();
app.use(express.json());               // parse JSON body (built-in)

app.get("/", (req, res) => res.send("Home"));
app.listen(3000, () => console.log("on 3000"));
```

## 2. Routing
```js
app.get("/users", (req, res) => res.json([{ id: 1 }]));
app.get("/users/:id", (req, res) => res.json({ id: req.params.id }));
app.post("/users", (req, res) => res.status(201).json(req.body));
app.put("/users/:id", (req, res) => res.json({ updated: req.params.id }));
app.delete("/users/:id", (req, res) => res.status(204).end());
```
- `req.params` = path vars (`@PathVariable`)
- `req.query` = query string (`@RequestParam`)
- `req.body` = parsed JSON (`@RequestBody`)

## 3. Middleware — the core concept (= Spring filters/interceptors)
A middleware is `(req, res, next) => {}`. It runs in order and calls `next()` to pass control.
```js
const logger = (req, res, next) => { console.log(req.method, req.url); next(); };
app.use(logger);                       // global
app.get("/admin", authCheck, handler); // route-specific
```

## 4. Routers — split routes (= multiple controllers)
```js
// routes/users.js
import { Router } from "express";
const r = Router();
r.get("/", (req,res)=>res.json([]));
export default r;
// app.js
import users from "./routes/users.js";
app.use("/users", users);
```

## 5. Error handling (4 args = error middleware)
```js
app.use((err, req, res, next) => {
  console.error(err);
  res.status(err.status || 500).json({ error: err.message });
});
```
Throw or `next(err)` from routes; this catches them. **SB parallel:** `@ControllerAdvice` / `@ExceptionHandler`.

## 6. Static files & CORS
```js
app.use(express.static("public"));
import cors from "cors"; app.use(cors());
```

## 7. Project structure (clean layers)
```
src/
  app.js
  routes/      -> controllers
  controllers/
  services/    -> business logic
  models/      -> data
  middleware/  -> auth, logging
```

## SB parallels
| Spring MVC | Express |
|---|---|
| @RestController | router + handlers |
| @GetMapping | app.get |
| @PathVariable | req.params |
| @RequestParam | req.query |
| @RequestBody | req.body |
| Filter/Interceptor | middleware |
| @ControllerAdvice | error middleware |

Next: `exercises.md`, then `07-REST-APIs`.
