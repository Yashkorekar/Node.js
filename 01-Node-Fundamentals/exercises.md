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

---
## Extra exercises (mix of concept + build)

7. **(build)** Read one line from stdin and print it uppercased.
8. Order these when queued together: `setTimeout(f,0)`, `setImmediate(f)`, `process.nextTick(f)`, a resolved `Promise.then(f)`.
9. **(build)** Print the CPU core count and total memory using the `os` module.
10. Exit the process with code `1` on error — what does a non-zero exit code signal to the OS / CI?
11. **(build)** A CLI `add.js` that reads two numeric args, validates them, and prints the sum (or an error + exit 1).
12. How do you log and then exit cleanly on an `uncaughtException`, and why is resuming after one risky?
13. What is the module wrapper Node puts around every file, and where do `require`, `module`, and `__dirname` come from?

### Answers
```js
// 7
process.stdin.once("data", d => console.log(d.toString().trim().toUpperCase()));
// 8  nextTick -> Promise.then (microtasks) -> then timers/setImmediate (macrotasks; their relative order varies).
// 9
const os = require("os"); console.log(os.cpus().length, os.totalmem());
// 10  Non-zero = failure; CI, shell `&&` chains, and orchestrators treat it as an error and stop.
// 11
const [a,b] = process.argv.slice(2).map(Number);
if (Number.isNaN(a) || Number.isNaN(b)) { console.error("need 2 numbers"); process.exit(1); }
console.log(a + b);
// 12  process.on("uncaughtException", e => { log(e); process.exit(1); }). State may be corrupt after one -> exit, don't resume.
// 13  Node wraps each file in (exports, require, module, __filename, __dirname) => {...}; that's why they exist without importing.
```
