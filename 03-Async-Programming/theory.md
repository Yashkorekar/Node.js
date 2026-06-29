# 03 — Async Programming (the heart of Node)

This is the #1 mindset shift from Spring Boot. Master it.

## 1. Why async?
Node is single-threaded. If you wait (block) on a DB call, no one else gets served. So I/O calls return immediately and run a callback **later** when done.

## 2. Callbacks (the old way)
```js
const fs = require("fs");
fs.readFile("a.txt", "utf8", (err, data) => {
  if (err) return console.error(err);
  console.log(data);
});
```
Nesting many → **callback hell**:
```js
getUser(id, (e,u)=>{ getOrders(u,(e,o)=>{ getItems(o,(e,i)=>{ /* 😵 */ }); }); });
```

## 3. Promises
A Promise is a future value: `pending → fulfilled | rejected`.
```js
function readFileP(path){
  return new Promise((resolve,reject)=>{
    fs.readFile(path,"utf8",(err,data)=> err?reject(err):resolve(data));
  });
}
readFileP("a.txt")
  .then(data => console.log(data))
  .catch(err => console.error(err))
  .finally(()=> console.log("done"));
```
**SB parallel:** `Promise` ≈ `CompletableFuture`. `.then` ≈ `.thenApply`, `.catch` ≈ `.exceptionally`.

## 4. async/await (the modern way)
```js
const fs = require("fs/promises");
async function load() {
  try {
    const data = await fs.readFile("a.txt", "utf8"); // looks sync, isn't
    console.log(data);
  } catch (err) {
    console.error(err);
  }
}
```
`await` pauses the function (not the thread) until the Promise settles. Reads top-to-bottom — much cleaner.

## 5. Running in parallel
```js
// Sequential (slow): 2x time
const a = await fetchA(); const b = await fetchB();
// Parallel (fast): both at once
const [a, b] = await Promise.all([fetchA(), fetchB()]);
// First to finish
const r = await Promise.race([fast(), slow()]);
// All settle regardless of failure
const results = await Promise.allSettled([fetchA(), fetchB()]);
```

## 6. Error handling rules
- Always wrap `await` in try/catch, or attach `.catch`.
- An unhandled rejection can crash the process. Don't ignore them.
- Return promises so callers can await; don't fire-and-forget unless intended.

## 7. Microtasks vs macrotasks (recap)
Promises (microtasks) run before timers (macrotasks). See folder 01 example.

## 8. Common gotcha — await in loops
```js
// Sequential
for (const id of ids) { await save(id); }
// Parallel
await Promise.all(ids.map(id => save(id)));
```

## SB parallels
| Java | Node |
|---|---|
| CompletableFuture | Promise |
| .thenApply | .then |
| .exceptionally | .catch |
| allOf | Promise.all |
| @Async | async function |

Next: `exercises.md`, then `04-Core-Modules`.
