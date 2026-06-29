# 09 — Exercises: Auth and Security

1. Hash a password on signup and verify on login (bcrypt).
2. Sign a JWT containing user id, expiring in 1h.
3. Write auth middleware returning 401 if token missing/invalid.
4. Add role check returning 403 for non-admins.
5. List 5 OWASP protections to add to an Express API.
6. Where do secrets live? How prevent brute force?
7. JWT vs session — when each?

---
## Answers
```js
// 1
const hash=await bcrypt.hash(pw,10); const ok=await bcrypt.compare(pw,hash);
// 2
jwt.sign({id},process.env.JWT_SECRET,{expiresIn:"1h"});
// 3
const t=req.headers.authorization?.split(" ")[1]; if(!t)return res.status(401).end(); try{req.user=jwt.verify(t,S);next()}catch{res.status(401).end()}
// 4
(req,res,next)=>req.user.role==="admin"?next():res.status(403).end();
// 5  input validation, helmet, rate limiting, CORS whitelist, parameterized queries, secrets in .env, https
// 6  .env + .gitignore; express-rate-limit to throttle attempts
// 7  JWT for stateless APIs/SPAs; sessions when you need easy revoke/logout & server state
```
