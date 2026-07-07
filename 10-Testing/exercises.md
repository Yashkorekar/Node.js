# 10 — Exercises: Testing

1. Write a Jest test for `add(2,2)===4`.
2. Test that a function throws on bad input.
3. Use beforeEach to reset state between tests.
4. Mock a service method to return a fixed value.
5. Supertest: assert GET /health returns 200 and `{status:"ok"}`.
6. Map JUnit → Jest: @Test, @BeforeEach, assertEquals, MockMvc.

---
## Answers
```js
// 1
test("add",()=>expect(add(2,2)).toBe(4));
// 2
expect(()=>parse("bad")).toThrow();
// 3
beforeEach(()=>{ store=[]; });
// 4
jest.spyOn(svc,"get").mockResolvedValue({id:1});
// 5
const r=await request(app).get("/health"); expect(r.status).toBe(200); expect(r.body).toEqual({status:"ok"});
// 6  test/it, beforeEach, expect().toBe, Supertest
```

---
## Extra exercises (mix of concept + build)

7. **(build)** `test.each`: parametrize `add()` over several input/expected pairs.
8. **(build)** Assert an async function rejects with a specific error.
9. `jest.mock("./mod")` vs `jest.spyOn(obj,"m")` — when each?
10. **(build)** Supertest: POST to create, then GET to assert it persisted.
11. Unit vs integration test — difference and when to prefer each.
12. How do you test code that uses `Date.now()` or `Math.random()` deterministically?
13. Is 100% coverage the goal? What does coverage actually tell you?
14. **(build)** `beforeAll`/`afterAll` to start and tear down an in-memory DB.

### Answers
```js
// 7
test.each([[1,1,2],[2,3,5]])("add(%i,%i)=%i",(a,b,e)=>expect(add(a,b)).toBe(e));
// 8
await expect(load("bad")).rejects.toThrow("not found");
// 9  jest.mock replaces a whole module (its exports); spyOn wraps one real method (and can restore it later).
// 10
await request(app).post("/todos").send({title:"x"}).expect(201);
const r=await request(app).get("/todos"); expect(r.body).toHaveLength(1);
// 11  Unit = one function with mocks, fast. Integration = real wiring (DB/HTTP), slower, higher confidence.
// 12  Inject a clock/RNG, or use jest.useFakeTimers() / jest.spyOn(Math,"random").
// 13  Coverage shows which lines ran, not that behavior is correct. Aim high on logic; don't chase a magic 100%.
// 14
beforeAll(async()=>{ await db.connect(mem.uri); });
afterAll(async()=>{ await db.disconnect(); await mem.stop(); });
```
