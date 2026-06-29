# 02 — Exercises: Modules and NPM

1. Create `calc.js` exporting `add` and `multiply`; import them in `app.js`.
2. Convert the same to ESM (export/import).
3. What does `^4.18.0` allow to install?
4. Which files should go in `.gitignore`? Which lockfile should be committed?
5. Add a `dev` script that restarts on file changes.
6. Difference between `dependencies` and `devDependencies`?

---
## Answers
1.
```js
// calc.js
module.exports = { add:(a,b)=>a+b, multiply:(a,b)=>a*b };
// app.js
const { add, multiply } = require("./calc");
```
2. `export const add=(a,b)=>a+b;` … `import { add } from "./calc.js";` (+ `"type":"module"`).
3. Any `4.x` up to `<5.0.0` (minor & patch updates).
4. Ignore `node_modules`, `.env`. Commit `package-lock.json`.
5. `"dev": "node --watch app.js"` or `nodemon app.js`.
6. `dependencies` ship to production; `devDependencies` are only for build/test (tools, test runners).
