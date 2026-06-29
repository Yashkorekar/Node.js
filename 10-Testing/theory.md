# 10 — Testing

Coming from JUnit, the concepts are familiar. Jest is the popular runner.

## 1. Jest setup
```bash
npm install -D jest supertest
```
package.json: `"scripts": { "test": "jest" }`

## 2. Unit tests
```js
// math.js
export const add = (a,b)=>a+b;
// math.test.js
import { add } from "./math.js";
test("adds", () => { expect(add(2,3)).toBe(5); });

describe("add", () => {
  it("handles negatives", () => expect(add(-1,-1)).toBe(-2));
});
```
**SB parallel:** `test`/`it` ≈ `@Test`; `expect().toBe` ≈ `assertEquals`; `describe` ≈ test class.

## 3. Matchers
```js
expect(x).toBe(5);          // ===
expect(obj).toEqual({a:1}); // deep
expect(arr).toContain(2);
expect(fn).toThrow();
expect(val).toBeNull();
await expect(promise).resolves.toBe(1);
```

## 4. Setup/teardown
```js
beforeAll(()=>{}); afterAll(()=>{});
beforeEach(()=>{}); afterEach(()=>{}); // @BeforeEach/@AfterEach
```

## 5. Mocking
```js
const fn = jest.fn().mockReturnValue(42);
jest.spyOn(svc, "save").mockResolvedValue({ id: 1 });
```

## 6. API tests with Supertest (= MockMvc)
```js
import request from "supertest";
import app from "../app.js";
test("GET /health", async () => {
  const res = await request(app).get("/health");
  expect(res.status).toBe(200);
  expect(res.body.status).toBe("ok");
});
test("POST /users", async () => {
  const res = await request(app).post("/users").send({ name:"A" });
  expect(res.status).toBe(201);
});
```

## 7. Coverage & structure
```bash
npx jest --coverage
```
Keep tests beside code or in `__tests__/`. Aim for: services unit-tested, routes integration-tested.

## SB parallels
| JUnit/Spring | Jest |
|---|---|
| @Test | test/it |
| assertEquals | expect().toBe |
| @BeforeEach | beforeEach |
| Mockito mock | jest.fn / spyOn |
| MockMvc | Supertest |

Next: `exercises.md`, then `11-Advanced`.
