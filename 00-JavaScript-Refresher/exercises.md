# 00 — Exercises: JavaScript Refresher

Try each before peeking. Answers at the bottom.

1. Convert `["a","bb","ccc"]` into `[1,2,3]` (lengths) using `map`.
2. Given `users = [{n:"A",active:true},{n:"B",active:false}]`, get names of active users.
3. Sum all even numbers in `[1..10]` using filter + reduce.
4. Destructure `{id:1, profile:{email:"x@y.com"}}` to a variable `email`.
5. Write an arrow function `isAdult(age)` returning true if age ≥ 18.
6. Use spread to merge `{a:1}` and `{b:2}` and override `a` to 9.
7. Safely read `cfg.db.host` where `cfg` might be undefined; default to `"localhost"`.
8. Explain difference between `==` and `===`. Which wins: `0 == ""`?

---
## Answers

```js
// 1
["a","bb","ccc"].map(s => s.length);            // [1,2,3]
// 2
users.filter(u => u.active).map(u => u.n);       // ["A"]
// 3
[1,2,3,4,5,6,7,8,9,10].filter(n=>n%2===0).reduce((s,n)=>s+n,0); // 30
// 4
const { profile: { email } } = { id:1, profile:{ email:"x@y.com" } };
// 5
const isAdult = age => age >= 18;
// 6
const m = { ...{a:1}, ...{b:2}, a:9 };           // {a:9,b:2}
// 7
const host = cfg?.db?.host ?? "localhost";
// 8  == coerces types, === doesn't. 0 == "" is TRUE (both coerce to 0). Always use ===.
```

---
## Extra exercises (mix of concept + build)

9. **(build)** Write `groupBy(arr, keyFn)` that turns `[{t:"a"},{t:"b"},{t:"a"}]` into `{a:[...], b:[...]}`.
10. Closures: `function counter(){let c=0;return ()=>++c;}` — what do two calls of the returned function print, and why does `c` survive after `counter()` returns?
11. Use `find`, `some`, and `every` on `users` to (a) get the first admin, (b) check if any is active, (c) check all have an email.
12. Does `const b = { ...a }` copy nested objects? How do you deep-clone safely?
13. **(build)** Write `pick(obj, keys)` returning a new object containing only the given keys.
14. Difference between `null` and `undefined`. What does `typeof null` return?
15. When does `this` differ between an arrow function and a regular `function`?
16. **(build)** Sketch `debounce(fn, ms)` that only calls `fn` after `ms` of no new calls.

### Answers
```js
// 9
const groupBy=(arr,f)=>arr.reduce((a,x)=>{(a[f(x)] ??= []).push(x);return a;},{});
// 10  1 then 2 — the inner arrow closes over `c`, which lives on in the closure after counter() returns.
// 11
users.find(u=>u.role==="admin"); users.some(u=>u.active); users.every(u=>u.email);
// 12  No — spread is a shallow copy; nested objects are shared. Deep clone: structuredClone(a).
// 13
const pick=(o,ks)=>Object.fromEntries(ks.filter(k=>k in o).map(k=>[k,o[k]]));
// 14  null = intentional "no value"; undefined = never assigned. typeof null === "object" (a historic bug).
// 15  Arrow `this` = the enclosing lexical scope's this; regular fn `this` = how it's called (the receiver).
// 16
const debounce=(fn,ms)=>{let t;return(...a)=>{clearTimeout(t);t=setTimeout(()=>fn(...a),ms);};};
```
