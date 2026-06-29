# 03 — Exercises: Async Programming

1. Output order? `console.log(1); setTimeout(()=>console.log(2)); Promise.resolve().then(()=>console.log(3)); console.log(4);`
2. Wrap `setTimeout` in a `delay(ms)` Promise; await it.
3. Convert this callback to async/await: `fs.readFile("x", "utf8", (e,d)=>{...})`.
4. Fetch 3 URLs in parallel and return all results.
5. Why is `for(const x of arr){ await f(x);}` slower than `Promise.all(arr.map(f))`?
6. What happens if a rejected Promise has no `.catch`?

---
## Answers
```js
// 1 -> 1 4 3 2
// 2
const delay = ms => new Promise(r => setTimeout(r, ms));
await delay(1000);
// 3
const fs = require("fs/promises");
const d = await fs.readFile("x", "utf8");
// 4
const data = await Promise.all([fetch(u1), fetch(u2), fetch(u3)].map(p=>p.then(r=>r.json())));
// 5  sequential awaits each finish before next; Promise.all starts all at once -> faster.
// 6  unhandled rejection -> warning, and can crash the process. Always handle.
```
