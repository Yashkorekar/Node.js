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

---
## Extra exercises (mix of concept + build)

7. `~1.2.3` vs `^1.2.3` — what version range does each allow?
8. **(build)** Add a `bin` field so your package runs as a CLI command.
9. ESM has no `__dirname`. How do you compute it? (`import.meta.url` + `fileURLToPath`).
10. **(build)** Conditionally load a module only when needed with dynamic `await import()`.
11. Module caching: if two files `require("./config")`, does the file run twice? Why does this make a module a natural singleton?
12. What is `npx` for? Give one case where it beats a global install.
13. `dependencies` vs `devDependencies` vs `peerDependencies` — one line each.

### Answers
```js
// 7  ~1.2.3 -> >=1.2.3 <1.3.0 (patches only). ^1.2.3 -> >=1.2.3 <2.0.0 (minor + patch).
// 8  package.json: "bin": { "yourtool": "./cli.js" }; add  #!/usr/bin/env node  at the top of cli.js.
// 9
import { fileURLToPath } from "url"; import { dirname } from "path";
const __dirname = dirname(fileURLToPath(import.meta.url));
// 10
if (needFast) { const { default: fast } = await import("./fast.js"); fast(); }
// 11  Runs once; the result is cached and reused -> everyone gets the same exported object = singleton.
// 12  Runs a package binary without a global install, e.g. `npx create-vite`; always uses the requested version.
// 13  dependencies: needed at runtime. devDependencies: build/test only. peerDependencies: the host app must provide it (plugins).
```
