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

---
## Extra exercises (mix of concept + build)

7. **(build)** Write `retry(fn, times)` that re-invokes an async `fn` up to `times` on rejection.
8. **(build)** Add a timeout to any promise using `Promise.race` (reject after `ms`).
9. Convert a callback API to a promise with `util.promisify`.
10. `Promise.all` vs `Promise.allSettled` when one input rejects — what does each return?
11. **(build)** Process 100 ids but run at most 3 in parallel (a concurrency limit).
12. What does `for await...of` do, and when would you use it?
13. **(build)** Cancel an in-flight `fetch` after 2s with `AbortController`.
14. Why does `try { doAsync(); } catch {}` NOT catch an error thrown inside `doAsync`?

### Answers
```js
// 7
const retry=async(fn,n)=>{try{return await fn();}catch(e){if(n<=1)throw e;return retry(fn,n-1);}};
// 8
const timeout=(p,ms)=>Promise.race([p,new Promise((_,r)=>setTimeout(()=>r(new Error("timeout")),ms))]);
// 9
const read=require("util").promisify(require("fs").readFile); await read("x","utf8");
// 10  all -> rejects on the first failure (you lose the others). allSettled -> resolves with {status, value|reason} for each.
// 11
async function pool(ids,limit,fn){const q=[...ids];await Promise.all(Array.from({length:limit},async()=>{while(q.length)await fn(q.shift());}));}
// 12  Iterates async iterables/streams, awaiting each value; use for paged APIs or readable streams.
// 13
const ac=new AbortController(); setTimeout(()=>ac.abort(),2000); await fetch(url,{signal:ac.signal});
// 14  You didn't await/return the promise — the rejection happens later, after the try block already exited.
```
