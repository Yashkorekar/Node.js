# 12 — Deployment, Tooling, and Capstone Projects

## 1. Environment config
```bash
npm install dotenv
```
```
# .env  (never commit)
PORT=3000
MONGO_URL=...
JWT_SECRET=...
```
```js
import "dotenv/config";
const port = process.env.PORT || 3000;
```

## 2. Production hardening
- `NODE_ENV=production`
- Run with PM2: `pm2 start app.js -i max --name api`
- Reverse proxy behind nginx for TLS/static.
- Logs to stdout (pino), let the platform collect them.
- Health endpoint `/health` for load balancers.

## 3. Dockerize
```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --omit=dev
COPY . .
EXPOSE 3000
CMD ["node", "src/app.js"]
```
```bash
docker build -t my-api . && docker run -p 3000:3000 --env-file .env my-api
```
**SB parallel:** like `java -jar app.jar` in a container; `npm ci` ≈ reproducible Maven build.

## 4. CI sketch
- install → lint → test → build image → deploy. Same shape as Maven CI.

## 5. Where to deploy
Render, Railway, Fly.io, Azure App Service, AWS, or a VPS + PM2 + nginx. Mongo Atlas / managed Postgres for DB.

---
## Capstone projects (build these, in order)
1. **CLI tool** — task list saved to JSON (folders 02–04).
2. **REST API** — products/users CRUD + validation (06–08).
3. **Auth-secured blog API** — JWT, bcrypt, roles, Mongo + tests (08–10).
4. **Real-time chat** — Express + socket.io + Redis (11).
5. **Full-stack app** — React/Vue frontend + your API + Docker deploy (12). Resume-ready.

## Capstone checklist (freelance-ready)
- [ ] Layered structure (routes/controllers/services/models)
- [ ] Validation + central error handling
- [ ] Auth (JWT + bcrypt), roles
- [ ] DB with indexes, pagination
- [ ] .env config, no secrets in git
- [ ] helmet, cors, rate limit
- [ ] tests (unit + Supertest)
- [ ] Dockerfile + health endpoint
- [ ] README with setup + API docs

## Keep going
- TypeScript, NestJS (very Spring-like!), GraphQL, message queues, observability.

You now have a complete path. Build the capstones — that's what lands the freelance work.
