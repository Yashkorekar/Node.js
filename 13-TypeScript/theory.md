# 13 — TypeScript for Node (the "level-up")

> Do this **after** you're comfortable with plain-JS Node (folders 00–12).
> TypeScript is **not required** to learn Node — Node runs JavaScript. But it's how most
> professional/freelance Node backends ship today, and for a Java dev it feels like coming home.

## 0. Why TypeScript (especially for a Java dev)
- Static types catch bugs **before** runtime — the "compile error, not 3am pager" feeling you know from Java.
- Editor autocomplete, refactors, and "go to definition" actually work on your whole codebase.
- Interfaces, generics, enums, access modifiers — concepts you already know from Java.
- It's a **superset** of JS: every valid `.js` is valid `.ts`. You adopt it gradually.

**SB parallel:** TS ≈ the type system + `javac`. `tsc` compiles `.ts` → `.js` (Node runs the `.js`).

## 1. Setup
```bash
npm install -D typescript @types/node tsx
npx tsc --init                 # creates tsconfig.json
```
- `tsc` = the compiler (type-check + emit JS).
- `tsx` = run `.ts` directly in dev (no separate build step): `npx tsx src/server.ts`.
- Build for prod: `npx tsc` → outputs `.js` into `dist/`, then `node dist/server.js`.

A sane `tsconfig.json` to start:
```jsonc
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "NodeNext",
    "moduleResolution": "NodeNext",
    "outDir": "dist",
    "rootDir": "src",
    "strict": true,          // turn this ON — it's the whole point
    "esModuleInterop": true,
    "skipLibCheck": true
  }
}
```

## 2. Basic types & inference
```ts
let name: string = "asha";
let age = 30;                 // inferred as number — don't annotate the obvious
const ids: number[] = [1, 2, 3];
let pair: [string, number] = ["x", 1];   // tuple
```
Prefer letting TS **infer**. Annotate function signatures and public boundaries, not every local.

## 3. Functions
```ts
function add(a: number, b: number): number { return a + b; }
const greet = (name: string, loud = false): string => loud ? `HI ${name}` : `hi ${name}`;
function log(msg: string, level?: "info" | "error"): void { /* optional param */ }
```

## 4. Objects, `interface` vs `type`
```ts
interface User { id: number; name: string; email?: string; }   // ? = optional
type Point = { x: number; y: number };                         // also fine
```
Rule of thumb: `interface` for object/class shapes (extendable, like a Java interface); `type` for unions, primitives, and computed types. **SB parallel:** `interface User` ≈ a POJO/record shape or a Java interface.

## 5. Union, literal & intersection types
```ts
type Status = "todo" | "doing" | "done";     // literal union — like a lightweight enum
type Id = string | number;                    // union
type Admin = User & { role: "admin" };        // intersection = combine
```

## 6. Enums (use sparingly — unions are often better)
```ts
enum Role { User, Admin }                      // Role.Admin
```

## 7. Narrowing & type guards
```ts
function len(x: string | string[]) {
  if (typeof x === "string") return x.length;  // narrowed to string here
  return x.length;                             // narrowed to string[] here
}
function isUser(x: unknown): x is User {        // custom type guard
  return typeof x === "object" && x !== null && "id" in x;
}
```

## 8. Generics (like Java generics)
```ts
function first<T>(arr: T[]): T | undefined { return arr[0]; }
interface ApiResponse<T> { data: T; error?: string; }
const r: ApiResponse<User> = { data: { id: 1, name: "a" } };
```

## 9. Utility types (huge time-savers)
```ts
Partial<User>          // all fields optional  (great for PATCH bodies)
Required<User>         // all fields required
Pick<User, "id">       // subset of fields
Omit<User, "email">    // everything except
Record<string, number> // dictionary/map type
Readonly<User>         // immutable view
```

## 10. `any` vs `unknown` vs `never`
- `any` = turns off type-checking. **Avoid.** It defeats the purpose.
- `unknown` = "I don't know yet" — safe; you must narrow before use.
- `never` = impossible value (e.g. a function that always throws / exhaustive checks).

## 11. Async types
```ts
async function load(id: number): Promise<User> {
  const res = await fetch(`/users/${id}`);
  return res.json() as Promise<User>;
}
```
An `async` function always returns `Promise<T>`. **SB parallel:** `Promise<User>` ≈ `CompletableFuture<User>`.

## 12. Classes & access modifiers (very Java-like)
```ts
class UserService {
  constructor(private readonly repo: UserRepo) {}   // param property = field + assignment
  async find(id: number): Promise<User> {
    return this.repo.byId(id);
  }
}
```
`public` / `private` / `protected` / `readonly` work like Java. The constructor-parameter shorthand is DI-friendly.

## 13. Typing Express
```bash
npm install express && npm install -D @types/express
```
```ts
import express, { Request, Response, NextFunction } from "express";
const app = express();
app.get("/users/:id", (req: Request, res: Response) => {
  const id = Number(req.params.id);
  res.json({ id });
});
// typed error middleware
app.use((err: Error, _req: Request, res: Response, _next: NextFunction) =>
  res.status(500).json({ error: err.message }));
```
Types for third-party libs live in `@types/*` packages (DefinitelyTyped). Modern libs (zod, Prisma) ship their own.

## 14. Validation that produces types (zod)
```ts
import { z } from "zod";
const UserDto = z.object({ name: z.string().min(1), age: z.number().int().min(0) });
type UserDto = z.infer<typeof UserDto>;         // type derived from the validator
const parsed = UserDto.parse(req.body);         // runtime-checked AND typed
```
This is the sweet spot: one source of truth for both runtime validation and static types.

## 15. Running & building
```bash
npx tsx src/server.ts          # dev: run TS directly
npx tsc --watch                # type-check on save
npx tsc                        # build to dist/
node dist/server.js            # prod
```

## SB parallels
| Java / Spring | TypeScript / Node |
|---|---|
| `javac` compile step | `tsc` compile step |
| `interface` / POJO / record | `interface` / `type` |
| Generics `List<T>` | Generics `T[]`, `Array<T>` |
| `enum` | `enum` or literal union |
| `Optional<T>` | `T \| undefined`, optional `?` |
| `CompletableFuture<T>` | `Promise<T>` |
| `private`/`protected`/`final` | `private`/`protected`/`readonly` |
| Bean validation annotations | zod / class-validator |

## When to reach for TS vs plain JS
- **Small script / learning a Node concept:** plain JS is faster to iterate.
- **Any real API you'll maintain, or freelance client work:** use TypeScript.

Next: `exercises.md`. That's the end of the path — from here, build the capstones in folder 12 **in TypeScript**.
