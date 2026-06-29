# Node.js Learning Path — From Beginner to Advanced

> Designed for a developer who already knows **Spring Boot / Java** well.
> Every topic includes **theory**, **Spring Boot parallels**, and **exercises with answers**.

---

## How to use this course

1. Go folder by folder, in order. Each folder is self-contained.
2. Read `theory.md` first. Type the code samples yourself — don't just read.
3. Do `exercises.md`. Try every question before opening the answers section at the bottom.
4. Build the mini-project at the end of each folder when one is given.
5. Don't skip the async folder (`03`). It's the heart of Node and the biggest mindset shift from Java.

**Pace suggestion:** 1 folder = roughly 1 focused study session. The whole path = a few weeks part-time. Enough to confidently take a freelance full-stack job.

---

## Folder map

| # | Folder | What you learn | SB analogy |
|---|--------|----------------|------------|
| 00 | `00-JavaScript-Refresher` | Modern JS you need before Node | Java language basics |
| 01 | `01-Node-Fundamentals` | Runtime, event loop, V8, globals | JVM + main thread model |
| 02 | `02-Modules-and-NPM` | CommonJS, ES Modules, package.json | Maven/Gradle + packages |
| 03 | `03-Async-Programming` | Callbacks, Promises, async/await | CompletableFuture / WebFlux |
| 04 | `04-Core-Modules` | fs, path, events, streams, buffers | java.io / java.nio |
| 05 | `05-HTTP-and-Servers` | Raw http server, requests, routing | Servlet / DispatcherServlet |
| 06 | `06-Express` | Routing, middleware, error handling | Spring MVC controllers/filters |
| 07 | `07-REST-APIs` | CRUD, validation, status codes, layers | @RestController + services |
| 08 | `08-Databases` | MongoDB (Mongoose) + SQL (Postgres) | JPA / Hibernate |
| 09 | `09-Auth-and-Security` | JWT, bcrypt, OWASP, helmet | Spring Security |
| 10 | `10-Testing` | Jest, Supertest, mocking | JUnit + MockMvc |
| 11 | `11-Advanced` | Cluster, worker threads, caching, WS | Threads, Redis, perf tuning |
| 12 | `12-Deployment-and-Projects` | env, Docker, capstone projects | jar/docker deploy |

---

## Toolbox checklist

- [ ] Node.js LTS installed (`node -v`, `npm -v`)
- [ ] VS Code + REST Client / Thunder Client extension
- [ ] A package manager (`npm`, or `pnpm`)
- [ ] MongoDB (local or Atlas) and PostgreSQL for folder 08
- [ ] Postman or curl for API testing

---

## Mindset shifts from Spring Boot to Node

| Spring Boot | Node.js |
|---|---|
| Multi-threaded, blocking by default | Single-threaded, non-blocking by default |
| Thread-per-request | Event loop + callbacks |
| Annotations & DI container | Plain functions, explicit `require`/`import` |
| Maven `pom.xml` | `package.json` |
| Tomcat embedded | You ARE the server (http/Express) |
| Hibernate/JPA | Mongoose / Prisma / Knex |
| Compiled, typed | Interpreted, dynamically typed (or TypeScript) |

Track your progress in `PROGRESS.md`.
