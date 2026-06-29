# 04 — Exercises: Core Modules

1. Build a cross-platform path to `<projectdir>/data/users.json`.
2. Write JSON to a file, then read & parse it back.
3. Create an EventEmitter that logs when a "signup" event fires with a username.
4. Copy a large file using streams (not readFile).
5. Hash the string "secret" with sha256.
6. Why prefer streams over `readFile` for a 2GB file?

---
## Answers
```js
// 1
const p = require("path").join(__dirname, "data", "users.json");
// 2
const fs = require("fs/promises");
await fs.writeFile("u.json", JSON.stringify({n:"A"}));
const o = JSON.parse(await fs.readFile("u.json","utf8"));
// 3
const E=require("events"); const b=new E(); b.on("signup",u=>console.log("hi",u)); b.emit("signup","asha");
// 4
require("fs").createReadStream("big").pipe(require("fs").createWriteStream("copy"));
// 5
require("crypto").createHash("sha256").update("secret").digest("hex");
// 6  streams use small chunks -> low memory; readFile loads all 2GB into RAM -> crash.
```
