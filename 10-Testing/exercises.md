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
