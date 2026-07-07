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

---
## Extra exercises (mix of concept + build)

8. Access vs refresh tokens — why two, and what's the refresh flow?
9. **(build)** Add `express-rate-limit` to the login route (5 attempts/min).
10. JWT in an httpOnly cookie vs `localStorage` — the security trade-off?
11. **(build)** A role middleware factory `requireRole("admin")`.
12. How do you prevent NoSQL / SQL injection in queries?
13. What does `helmet` do? Name three headers it sets.
14. **(build)** Outline a password-reset flow: token, expiry, single-use.
15. Why does bcrypt's cost factor matter, and what's a reasonable value?

### Answers
```js
// 8  Short-lived access token + long-lived refresh token. On 401, POST the refresh token to mint a new access token.
// 9
import rateLimit from "express-rate-limit";
app.post("/login", rateLimit({windowMs:60000, max:5}), loginHandler);
// 10  httpOnly cookie = JS can't read it (blocks XSS token theft) but needs CSRF protection. localStorage = XSS-readable.
// 11
const requireRole=role=>(req,res,next)=>req.user?.role===role?next():res.status(403).end();
// 12  Never interpolate input: use parameterized queries (SQL) and validate/cast types before Mongo queries.
// 13  Sets security headers: Content-Security-Policy, X-Content-Type-Options, Strict-Transport-Security, etc.
// 14  Create a random token, store its hash + expiry, email the link, verify + consume once, then delete it.
// 15  Higher cost = slower hash = harder brute force. ~10-12 is a good balance today.
```
