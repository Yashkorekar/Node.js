# 12 — Exercises: Deployment & Projects

1. Load PORT from env with a 3000 fallback.
2. Write a minimal Dockerfile for a Node API.
3. Why `npm ci` over `npm install` in CI?
4. Name 3 production hardening steps.
5. List the layered folders a clean API should have.
6. Pick a capstone and write its 5-line feature list.

---
## Answers
1. `import "dotenv/config"; const port=process.env.PORT||3000;`
2. See theory Dockerfile (node:20-alpine, copy, npm ci, CMD node app).
3. `npm ci` is clean, lockfile-exact, reproducible; install may mutate versions.
4. NODE_ENV=production, PM2/cluster, nginx TLS, health checks, structured logs (any 3).
5. routes, controllers, services, models, middleware.
6. e.g. Blog API: register/login (JWT), CRUD posts, comments, roles, pagination + tests.
