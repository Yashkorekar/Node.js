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

---
## Extra exercises (mix of concept + build)

7. **(build)** Write a multi-stage Dockerfile (builder installs + builds, slim runtime image).
8. **(build)** A `docker-compose.yml` running the app + MongoDB + Redis.
9. **(build)** Validate required env vars at startup and fail fast (sketch with zod).
10. Health/liveness vs readiness probe — what's the difference?
11. List a minimal CI pipeline from push to deploy.
12. Name two zero-downtime deployment strategies.
13. **(build)** Capstone spec: pick one project and write its endpoints, data model, auth, and test checklist.

### Answers
```dockerfile
# 7  Dockerfile — multi-stage
FROM node:20-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --omit=dev
COPY --from=build /app/dist ./dist
CMD ["node","dist/server.js"]
```
```yaml
# 8  docker-compose.yml
services:
  app:   { build: ., ports: ["3000:3000"], env_file: .env, depends_on: [mongo, redis] }
  mongo: { image: mongo:7, ports: ["27017:27017"] }
  redis: { image: redis:7, ports: ["6379:6379"] }
```
```js
// 9
import { z } from "zod";
const Env = z.object({ PORT: z.coerce.number().default(3000), DATABASE_URL: z.string().url(), JWT_SECRET: z.string().min(16) });
export const env = Env.parse(process.env);   // throws & exits at boot if anything is missing/invalid
// 10  Liveness/health = "is the process up?"; readiness = "can it serve traffic yet (DB connected, warmed up)?".
// 11  push -> install (npm ci) -> lint -> test -> build -> build & push Docker image -> deploy.
// 12  Blue-green (swap two identical envs) and rolling (replace instances a few at a time). Canary is a third.
// 13  e.g. Notes API: POST/GET/PUT/DELETE /notes, User + Note models, JWT auth + ownership checks, tests for CRUD + auth + validation.
```
