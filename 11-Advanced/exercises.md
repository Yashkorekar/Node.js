# 11 — Exercises: Advanced

1. Why does CPU-heavy work hurt Node? Two fixes?
2. Fork one worker per CPU core with cluster.
3. Cache a user in Redis with 60s TTL, then read it.
4. Echo broadcast a chat message to all clients with socket.io.
5. Pipe a file through a transform to another file with error handling.
6. List 4 ways to speed up a slow Express API.
7. How to shut down gracefully on SIGTERM?

---
## Answers
1. It blocks the single event loop. Fixes: worker_threads, cluster/PM2, offload to queue.
2. `os.cpus().forEach(()=>cluster.fork())` in primary.
3. `await r.set("user:1",JSON.stringify(u),"EX",60); await r.get("user:1");`
4. `s.on("msg",m=>io.emit("msg",m));`
5. `await pipeline(src, transform, dest);`
6. indexes, pagination, parallel Promise.all, caching, gzip.
7. `process.on("SIGTERM",()=>server.close(()=>db.close()))`.

---
## Extra exercises (mix of concept + build)

8. **(build)** Offload a CPU-heavy `fib(40)` to a worker thread so the event loop stays free.
9. Why push a slow task onto a job queue (BullMQ/Redis) instead of doing it inside the request?
10. Cache-aside vs write-through — what's the difference?
11. How would you detect and diagnose a memory leak? Name a tool.
12. **(build)** Measure event-loop lag with a simple timer-drift check.
13. Cluster + WebSockets across workers: what breaks, and what's the fix?
14. Backpressure: what is it, and how does `pipeline` handle it for you?

### Answers
```js
// 8
import { Worker } from "worker_threads";
const w=new Worker("./fib.js",{workerData:40}); w.on("message",r=>console.log(r));
// 9  Keeps requests fast (respond now, process later), survives restarts, and adds retries + rate limiting.
// 10  Cache-aside: app fills the cache on a miss. Write-through: writes hit cache + DB together (cache always fresh).
// 11  Watch RSS grow over time; take heap snapshots with --inspect / clinic.js / heapdump and diff them.
// 12
let last=Date.now(); setInterval(()=>{const d=Date.now()-last-500;last=Date.now();console.log("lag",d);},500);
// 13  A socket lives on one worker only, so broadcasts miss the others. Fix: a shared adapter (socket.io Redis adapter) + sticky sessions.
// 14  Backpressure = a slow consumer telling the producer to pause. pipeline() honors it and cleans up on error.
```
