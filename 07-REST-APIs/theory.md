# 07 — Building REST APIs (the freelance bread & butter)

## 1. REST principles
- Resources are nouns: `/products`, `/users/42/orders`.
- HTTP verbs = actions: GET (read), POST (create), PUT/PATCH (update), DELETE.
- Stateless: each request carries everything (token), no server session needed.
- Status codes reflect outcome (200/201/204/400/404/500).

## 2. Layered architecture (like Spring)
```
route (controller) -> service (logic) -> model/repo (data)
```
```
src/
  routes/product.routes.js   // map URLs
  controllers/product.ctrl.js// parse req, send res
  services/product.svc.js    // business rules
  models/product.model.js    // data access
```

## 3. CRUD example (in-memory for now)
```js
// product.svc.js
let items = []; let id = 1;
export const list = () => items;
export const get = (i) => items.find(p => p.id == i);
export const create = (d) => { const p={id:id++,...d}; items.push(p); return p; };
export const update = (i,d) => { const p=get(i); if(p)Object.assign(p,d); return p; };
export const remove = (i) => { items = items.filter(p=>p.id!=i); };
```
```js
// product.routes.js
import { Router } from "express"; import * as s from "../services/product.svc.js";
const r = Router();
r.get("/", (req,res)=>res.json(s.list()));
r.get("/:id",(req,res)=>{const p=s.get(req.params.id); p?res.json(p):res.status(404).json({error:"not found"});});
r.post("/",(req,res)=>res.status(201).json(s.create(req.body)));
r.put("/:id",(req,res)=>res.json(s.update(req.params.id,req.body)));
r.delete("/:id",(req,res)=>{s.remove(req.params.id);res.status(204).end();});
export default r;
```

## 4. Validation (use a schema lib)
```bash
npm install zod
```
```js
import { z } from "zod";
const schema = z.object({ name: z.string().min(1), price: z.number().positive() });
const result = schema.safeParse(req.body);
if (!result.success) return res.status(400).json({ errors: result.error.issues });
```
**SB parallel:** Zod ≈ Bean Validation (`@NotNull`, `@Min`). Joi is another option.

## 5. Consistent responses & errors
```js
res.status(200).json({ data: items });
res.status(400).json({ error: { message: "Invalid", details } });
```

## 6. Pagination, filtering, sorting
```js
const { page = 1, limit = 10, sort } = req.query;
```

## 7. Async controllers + central error handler
```js
const wrap = fn => (req,res,next)=>Promise.resolve(fn(req,res,next)).catch(next);
r.get("/", wrap(async (req,res)=>res.json(await s.list())));
```

## 8. API testing
Use curl/Thunder Client:
```bash
curl localhost:3000/products
curl -X POST localhost:3000/products -H "Content-Type: application/json" -d '{"name":"Pen","price":5}'
```

Next: `exercises.md`, then `08-Databases`.
