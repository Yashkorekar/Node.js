# 02 — Modules and NPM

## 1. Two module systems
### CommonJS (CJS) — classic Node
```js
// math.js
function add(a, b) { return a + b; }
module.exports = { add };          // export
// app.js
const { add } = require("./math"); // import
```
### ES Modules (ESM) — modern, browser-aligned
```js
// math.mjs (or set "type":"module" in package.json)
export function add(a, b) { return a + b; }
export default function () {}
// app.mjs
import { add } from "./math.js";
```
Use ESM for new projects (`"type": "module"`). CJS still everywhere in tutorials.

**SB parallel:** `require`/`import` ≈ Java `import`, but here imports run files (side effects matter) and there's no auto-wiring.

## 2. package.json — your pom.xml
```bash
npm init -y          # create package.json
```
```json
{
  "name": "my-app",
  "version": "1.0.0",
  "type": "module",
  "main": "app.js",
  "scripts": { "start": "node app.js", "dev": "node --watch app.js" },
  "dependencies": {},
  "devDependencies": {}
}
```
Run scripts: `npm start`, `npm run dev`.

## 3. npm commands
```bash
npm install express          # add a dependency
npm install -D nodemon       # dev dependency
npm install                  # install everything in package.json
npm uninstall express
npm update
npm list --depth=0           # what's installed
```
- `node_modules/` = downloaded packages (never commit; add to `.gitignore`).
- `package-lock.json` = exact versions (commit it).

## 4. Semantic versioning
`^1.2.3` = minor+patch updates; `~1.2.3` = patch only; `1.2.3` = exact. (Like Maven version ranges.)

## 5. Built-in vs local vs package
```js
const fs = require("fs");          // built-in core module
const { add } = require("./math"); // your file (relative path)
const express = require("express");// from node_modules
```

## 6. A clean folder layout
```
my-app/
  src/
    index.js
    routes/
    services/
  package.json
  .gitignore   (node_modules, .env)
```

## SB parallels
| Maven/Gradle | npm |
|---|---|
| pom.xml | package.json |
| ~/.m2 repo | node_modules |
| version ranges | ^ ~ semver |
| `mvn package` | `npm run build` |
| profiles | scripts |

Next: `exercises.md`, then `03-Async-Programming`.
