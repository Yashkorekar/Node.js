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
