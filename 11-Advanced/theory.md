# 11 — Advanced Node.js & Performance

## 1. The single-thread limitation
CPU-heavy work blocks the event loop. Solutions: cluster, worker threads, offload to a queue.

## 2. Cluster — use all CPU cores
```js
import cluster from "cluster"; import os from "os";
if (cluster.isPrimary) {
  os.cpus().forEach(() => cluster.fork());        // 1 worker per core
} else {
  startServer();                                  // each worker runs the app
}
```
Or use **PM2** in production: `pm2 start app.js -i max`. **SB parallel:** like running multiple JVM instances behind a load balancer.

## 3. Worker threads — CPU tasks off the main loop
```js
import { Worker } from "worker_threads";
const w = new Worker("./heavy.js");
w.postMessage(bigInput);
w.on("message", result => console.log(result));
```

## 4. Caching with Redis
```bash
npm install ioredis
```
```js
import Redis from "ioredis"; const r = new Redis();
await r.set("user:1", JSON.stringify(u), "EX", 60); // 60s TTL
const c = await r.get("user:1");
```
Cache-aside: check cache → miss → DB → store. **SB parallel:** like Spring Cache + Redis.

## 5. WebSockets (real-time)
```bash
npm install socket.io
```
```js
import { Server } from "socket.io"; const io = new Server(server);
io.on("connection", s => { s.on("msg", m => io.emit("msg", m)); });
```
Great for chat, dashboards, notifications — places Spring WebSocket would fit.

## 6. Streams & backpressure
Pipe big data; respect backpressure so memory stays flat. Use `pipeline()` to handle errors:
```js
import { pipeline } from "stream/promises";
await pipeline(src, transform, dest);
```

## 7. Performance tips
- Never block: use async I/O everywhere on the hot path.
- Use `Promise.all` for parallel calls.
- Add DB indexes; paginate.
- gzip with `compression`; cache hot reads.
- Profile with `--prof`, clinic.js, or `node --inspect`.
- Avoid sync fs/crypto on request path.

## 8. Reliability
- Graceful shutdown: handle SIGTERM, close server + DB.
- Logging: `pino`/`winston`. Don't log secrets.
- Handle `unhandledRejection`/`uncaughtException` and exit cleanly.

## 9. TypeScript (worth learning next)
Brings Java-like static types to Node — great for big freelance projects.
```bash
npm install -D typescript @types/node && npx tsc --init
```

## SB parallels
| Spring | Node |
|---|---|
| thread pool / instances | cluster / PM2 |
| @Async / executor | worker_threads |
| Spring Cache+Redis | ioredis cache-aside |
| Spring WebSocket | socket.io |
| Actuator health | custom /health + pino |

Next: `exercises.md`, then `12-Deployment-and-Projects`.
