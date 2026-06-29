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
