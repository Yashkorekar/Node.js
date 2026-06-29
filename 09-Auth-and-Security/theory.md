# 09 — Authentication and Security

Your Spring Security knowledge transfers conceptually; the tools differ.

## 1. Password hashing — bcrypt
Never store plaintext passwords. Hash + salt them.
```bash
npm install bcrypt
```
```js
import bcrypt from "bcrypt";
const hash = await bcrypt.hash(password, 10);   // on signup
const ok = await bcrypt.compare(password, hash); // on login
```

## 2. JWT (stateless tokens)
```bash
npm install jsonwebtoken
```
```js
import jwt from "jsonwebtoken";
const token = jwt.sign({ id: user.id, role: user.role }, process.env.JWT_SECRET, { expiresIn: "1h" });
const payload = jwt.verify(token, process.env.JWT_SECRET); // throws if invalid
```
Client sends it: `Authorization: Bearer <token>`. **SB parallel:** like Spring Security JWT filter; payload ≈ Authentication principal.

## 3. Auth middleware (= security filter)
```js
function auth(req, res, next) {
  const token = req.headers.authorization?.split(" ")[1];
  if (!token) return res.status(401).json({ error: "no token" });
  try { req.user = jwt.verify(token, process.env.JWT_SECRET); next(); }
  catch { return res.status(401).json({ error: "invalid token" }); }
}
function requireRole(role){ return (req,res,next)=> req.user.role===role?next():res.status(403).end(); }
app.get("/admin", auth, requireRole("admin"), handler);
```

## 4. Login flow
```js
app.post("/login", async (req,res)=>{
  const u = await User.findOne({ email: req.body.email });
  if(!u || !(await bcrypt.compare(req.body.password, u.password))) return res.status(401).json({error:"bad creds"});
  res.json({ token: jwt.sign({id:u.id}, process.env.JWT_SECRET, {expiresIn:"1h"}) });
});
```

## 5. OWASP essentials checklist
- **Validate all input** (Zod/Joi). Never trust client data.
- **NoSQL/SQL injection**: use parameterized queries / ORM; never interpolate user input.
- **helmet** sets safe headers: `app.use(helmet())`.
- **CORS**: whitelist origins.
- **Rate limit**: `express-rate-limit` to stop brute force.
- **Secrets in .env**, never in git. Use `.gitignore`.
- **HTTPS** in production.
- Don't leak stack traces in responses.
```bash
npm install helmet express-rate-limit cors
```
```js
import helmet from "helmet"; import rateLimit from "express-rate-limit";
app.use(helmet());
app.use(rateLimit({ windowMs: 60000, max: 100 }));
```

## 6. Sessions vs JWT
- JWT: stateless, scales, good for APIs/SPAs.
- Sessions (`express-session` + Redis): server-stored, easy logout/revoke.

## SB parallels
| Spring Security | Node |
|---|---|
| BCryptPasswordEncoder | bcrypt |
| JWT filter | auth middleware |
| @PreAuthorize("hasRole") | requireRole |
| SecurityFilterChain | app.use(...) order |

Next: `exercises.md`, then `10-Testing`.
