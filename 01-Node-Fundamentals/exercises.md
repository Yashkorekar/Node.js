# 01 — Exercises: Node Fundamentals

1. Predict the output: `console.log("A"); setTimeout(()=>console.log("B")); Promise.resolve().then(()=>console.log("C")); console.log("D");`
2. Write a program that prints a name passed as a CLI arg, defaulting to "guest".
3. Print all environment variable names.
4. Why is a huge synchronous loop dangerous in Node but fine-ish in Spring Boot?
5. What does `__dirname` give you and how is it different from `process.cwd()`?
6. Name three I/O-heavy use cases where Node shines.

---
## Answers
1. `A D C B` (sync, then promise microtask, then timer macrotask).
2. `node app.js` → `const n = process.argv[2] ?? "guest"; console.log(n);`
3. `console.log(Object.keys(process.env));`
4. Node has ONE thread for JS; blocking it stalls every request. Spring Boot spreads requests across many threads.
5. `__dirname` = folder of the current file; `process.cwd()` = where you launched `node` from.
6. REST APIs, chat/realtime (WebSockets), file/stream proxies, aggregating other APIs.
