# 04 — Core (Built-in) Modules

Node ships with batteries. These are imported without npm.

## 1. path — never hardcode slashes
```js
const path = require("path");
path.join(__dirname, "data", "a.txt"); // OS-safe path
path.basename("/x/y/a.txt");           // a.txt
path.extname("a.txt");                 // .txt
path.resolve("a", "b");                // absolute
```

## 2. fs — file system (use promises)
```js
const fs = require("fs/promises");
await fs.writeFile("note.txt", "hello");
const txt = await fs.readFile("note.txt", "utf8");
await fs.appendFile("note.txt", "\nmore");
await fs.mkdir("logs", { recursive: true });
const files = await fs.readdir(".");
await fs.unlink("note.txt"); // delete
```
There's also sync versions (`fs.readFileSync`) — fine in scripts, avoid in servers (they block).

## 3. os & process
```js
const os = require("os");
os.cpus().length; os.totalmem(); os.platform();
process.env.PORT; process.argv; process.pid;
```

## 4. EventEmitter — the pub/sub backbone
```js
const EventEmitter = require("events");
const bus = new EventEmitter();
bus.on("order", o => console.log("got", o));  // subscribe
bus.emit("order", { id: 1 });                  // publish
bus.once("ready", () => console.log("once"));  // one-shot
```
**SB parallel:** like Spring's `ApplicationEventPublisher` / `@EventListener`.

## 5. Streams — process data in chunks (no full load)
```js
const fs = require("fs");
const rs = fs.createReadStream("big.txt");
const ws = fs.createWriteStream("copy.txt");
rs.pipe(ws);                       // copy without loading all into RAM
rs.on("data", c => /* chunk */ {});
rs.on("end", () => console.log("done"));
```
Four kinds: Readable, Writable, Duplex, Transform. Crucial for big files & video. **SB parallel:** like `InputStream`/`OutputStream`.

## 6. Buffer — raw bytes
```js
const b = Buffer.from("Hi");
b.toString("hex"); // 4869
```
Used for binary data, hashing, file/network bytes.

## 7. crypto — hashing
```js
const crypto = require("crypto");
crypto.createHash("sha256").update("data").digest("hex");
crypto.randomUUID();
```

## 8. util
```js
const { promisify } = require("util");
const sleep = promisify(setTimeout);
await sleep(500);
```

Next: `exercises.md`, then `05-HTTP-and-Servers`.
