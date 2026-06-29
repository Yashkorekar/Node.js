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
