# 13 — Exercises: TypeScript for Node

Try each before peeking. Answers at the bottom. Mix of concept + build.

1. Type a function `sum(nums)` that takes an array of numbers and returns a number.
2. Define an `interface Product` with `id: number`, `name: string`, optional `price`.
3. **(build)** Write a generic `first<T>(arr)` that returns the first item or `undefined`.
4. What's the difference between `any` and `unknown`? Which is safe?
5. Model a `Status` that can only be `"open" | "closed"` (no other value allowed).
6. **(build)** Use `Partial<Product>` to type a `patchProduct(id, changes)` function.
7. Write a type guard `isString(x: unknown): x is string`.
8. **(build)** Type an async function `getUser(id: number)` that resolves to a `User`.
9. Why prefer a literal union over an `enum` in many cases?
10. **(build)** Type an Express handler for `GET /items/:id` (params + response).
11. Given `const UserDto = z.object({...})`, how do you get a TS type from it?
12. Convert this JS class to typed TS with a `private readonly` dependency:
    `class Svc { constructor(repo){ this.repo = repo; } }`

---
## Answers
```ts
// 1
function sum(nums: number[]): number { return nums.reduce((a, b) => a + b, 0); }
// 2
interface Product { id: number; name: string; price?: number; }
// 3
function first<T>(arr: T[]): T | undefined { return arr[0]; }
// 4  any = type-checking off (unsafe). unknown = must narrow before use (safe). Prefer unknown.
// 5
type Status = "open" | "closed";
// 6
function patchProduct(id: number, changes: Partial<Product>): void { /* merge changes */ }
// 7
function isString(x: unknown): x is string { return typeof x === "string"; }
// 8
async function getUser(id: number): Promise<User> { return db.users.byId(id); }
// 9  Literal unions are lighter, need no import, narrow cleanly, and don't emit runtime code like enums do.
// 10
import { Request, Response } from "express";
app.get("/items/:id", (req: Request, res: Response) => {
  const id = Number(req.params.id);
  res.json({ id });
});
// 11
type UserDto = z.infer<typeof UserDto>;
// 12
class Svc {
  constructor(private readonly repo: UserRepo) {}
}
```

### Mini-project (optional)
Rebuild the folder 07 REST `tasks` API in TypeScript:
- `interface Task { id: number; title: string; status: "todo" | "done"; }`
- zod schema for the create body → derive the type with `z.infer`.
- Typed controller → service → in-memory repo layers.
- Run it in dev with `tsx`, then `tsc` build to `dist/` and run with `node`.
