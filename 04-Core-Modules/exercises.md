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

---
## Extra exercises (mix of concept + build)

7. **(build)** Recursively list all `.js` files under a directory.
8. **(build)** Count the lines in a large file with `readline` over a stream.
9. Gzip a file using `zlib` + `pipeline`.
10. **(build)** Run a shell command with `child_process` and capture stdout.
11. Buffers: convert a string to base64 and back.
12. Parse a URL into protocol, host, pathname, and a query param.
13. **(build)** Write a `Transform` stream that upper-cases text passing through.

### Answers
```js
// 7
import { readdir } from "fs/promises"; import { join } from "path";
async function walk(dir){const out=[];for(const e of await readdir(dir,{withFileTypes:true})){const p=join(dir,e.name);if(e.isDirectory())out.push(...await walk(p));else if(p.endsWith(".js"))out.push(p);}return out;}
// 8
import readline from "readline"; import fs from "fs";
let n=0; const rl=readline.createInterface({input:fs.createReadStream("big.txt")});
rl.on("line",()=>n++); rl.on("close",()=>console.log(n));
// 9
import { createGzip } from "zlib"; import { pipeline } from "stream/promises";
await pipeline(fs.createReadStream("a.txt"), createGzip(), fs.createWriteStream("a.txt.gz"));
// 10
import { execFile } from "child_process";
execFile("node",["-v"],(e,out)=>console.log(out.trim()));
// 11
const b=Buffer.from("hi").toString("base64"); Buffer.from(b,"base64").toString(); // "hi"
// 12
const u=new URL("https://x.com/p?q=1"); u.protocol; u.host; u.pathname; u.searchParams.get("q");
// 13
import { Transform } from "stream";
const up=new Transform({transform(c,_,cb){cb(null,c.toString().toUpperCase());}});
```
