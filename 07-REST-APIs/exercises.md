# 07 — Exercises: REST APIs

1. Which verb+status for: create user, fetch all, delete, not found?
2. Design routes for a `tasks` resource (CRUD).
3. Add validation: name required, dueDate must be a date.
4. Return 404 JSON when a task id doesn't exist.
5. Add `?page=&limit=` pagination to the list endpoint.
6. Write a wrapper that forwards async errors to the error middleware.

---
## Answers
1. POST→201, GET→200, DELETE→204, missing→404.
2. `GET /tasks, GET /tasks/:id, POST /tasks, PUT /tasks/:id, DELETE /tasks/:id`.
3. `z.object({ name:z.string().min(1), dueDate:z.coerce.date() })` then `safeParse` → 400 on fail.
4. `const t=get(id); if(!t) return res.status(404).json({error:"not found"});`
5. `const {page=1,limit=10}=req.query; items.slice((page-1)*limit, page*limit);`
6. `const wrap=fn=>(req,res,next)=>Promise.resolve(fn(req,res,next)).catch(next);`

---
## Extra exercises (mix of concept + build)

7. `PUT` vs `PATCH` — which is idempotent, and what does each mean semantically?
8. **(build)** Add filtering `?status=done` and sorting `?sort=-createdAt` to a list endpoint.
9. Design a consistent error body shape and use it everywhere.
10. Two ways to version an API — trade-offs of each.
11. **(build)** Validate a request body with zod and return 400 with field-level errors.
12. **(build)** Split one route into controller → service → repository (in-memory).
13. `400` vs `422` vs `404` vs `409` — when do you use each?

### Answers
```js
// 7  PUT replaces the whole resource (idempotent). PATCH applies a partial update (not necessarily idempotent).
// 8
let r=tasks.filter(t=>!status||t.status===status);
if(sort){const d=sort[0]==="-"?-1:1,k=sort.replace("-","");r.sort((a,b)=>(a[k]>b[k]?1:-1)*d);}
// 9  { error: { code: "VALIDATION", message: "...", details: [...] } } — same shape for every failure.
// 10  URL path (/v1/...) = simple, cacheable, visible. Header (Accept-Version) = clean URLs but harder to test.
// 11
const p=schema.safeParse(req.body); if(!p.success) return res.status(400).json({errors:p.error.flatten()});
// 12  controller reads req -> calls service (logic) -> calls repo (data). The route stays thin.
// 13  400 malformed, 422 valid JSON but bad values, 404 not found, 409 conflict (e.g. duplicate).
```
