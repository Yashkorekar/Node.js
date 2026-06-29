# 01 — Node.js Fundamentals

## 1. What is Node.js?
Node.js is a **runtime** that runs JavaScript outside the browser using Google's **V8** engine plus a C++ library called **libuv** that gives async I/O and the event loop.

- It is **single-threaded** for your JS code, but I/O is offloaded to a thread pool / OS, so it's non-blocking.
- Great for I/O-heavy apps (APIs, real-time, streaming). Less ideal for CPU-heavy number crunching (covered in folder 11).

**SB parallel:** Spring Boot uses a thread-per-request model on a pool (Tomcat). Node uses **one** main thread + an event loop, so blocking that thread blocks everyone. Different mental model.

## 2. Run code
```bash
node -v            # check version
node app.js        # run a file
node               # REPL (interactive shell)
```

## 3. The event loop (the core idea)
```
   ┌───────────────┐
   │ Call Stack    │  your sync code runs here
   └──────┬────────┘
          │ async ops (timers, fs, http) sent to libuv
   ┌──────▼────────┐
   │ Event Loop    │  picks finished callbacks
   └──────┬────────┘
   ┌──────▼────────┐
   │ Callback Queue│  microtasks (promises) before macrotasks (timers)
   └───────────────┘
```
```js
console.log("1");
setTimeout(() => console.log("2"), 0);   // macrotask
Promise.resolve().then(() => console.log("3")); // microtask
console.log("4");
// Output: 1 4 3 2
```
**Rule:** sync first → microtasks (Promises) → macrotasks (timers). Never block the loop with heavy sync loops.

## 4. Globals (no `window` here)
```js
__dirname     // absolute dir of current file
__filename    // absolute file path
process       // env, argv, exit — the running process
console       // log/error/warn
setTimeout / setInterval / setImmediate
global        // global object (like window in browser)
```
```js
console.log(process.argv);        // CLI args
console.log(process.env.HOME);    // env vars
process.exit(0);                  // exit
```

## 5. First program
```js
// app.js
const name = process.argv[2] ?? "world";
console.log(`Hello, ${name}!`);
```
```bash
node app.js Asha   # Hello, Asha!
```

## 6. Why "don't block the event loop"
```js
// BAD: blocks everything for seconds
for (let i = 0; i < 1e10; i++) {}
// While this runs, no other request is served. In Spring Boot another thread would handle them.
```

## SB parallels
| Spring Boot | Node |
|---|---|
| JVM | V8 + libuv |
| Tomcat thread pool | single event loop thread |
| `application.properties` | `process.env` / .env |
| `main()` | the top of your `.js` file |

Next: `exercises.md`, then `02-Modules-and-NPM`.
