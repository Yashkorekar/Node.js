# 08 — Exercises: Databases

1. Define a Mongoose User schema: name(required), email(unique), age(min 0).
2. Find users older than 18, sorted by name, limit 5.
3. Mongoose: create, then update price, then delete a product.
4. Populate an order's user reference.
5. Prisma: model a `Post` with id, title, published(bool, default false).
6. Where should DB credentials live, and how do you connect once?
7. When choose MongoDB vs Postgres?

---
## Answers
```js
// 1
new mongoose.Schema({name:{type:String,required:true},email:{type:String,unique:true},age:{type:Number,min:0}});
// 2
await User.find({age:{$gt:18}}).sort({name:1}).limit(5);
// 3
const p=await P.create({name:"x",price:5}); await P.updateOne({_id:p._id},{price:6}); await P.deleteOne({_id:p._id});
// 4
await Order.findById(id).populate("user");
// 5  model Post { id Int @id @default(autoincrement()) title String published Boolean @default(false) }
// 6  in .env via dotenv; connect once at startup, reuse the connection.
// 7  Mongo for flexible docs/fast iteration; Postgres for relations, transactions, reporting.
```

---
## Extra exercises (mix of concept + build)

8. **(build)** Mongoose aggregation: count orders per user.
9. Add a unique index and a compound index — why each?
10. `populate` (reference) vs embedding — trade-offs.
11. **(build)** Raw SQL: join users and orders and count orders per user.
12. What is the N+1 query problem, and how do you avoid it?
13. Transactions: transfer money between two accounts atomically (why one write isn't enough).
14. Why parameterized queries instead of string concatenation?
15. **(build)** Prisma: model a one-to-many between `User` and `Post`.

### Answers
```js
// 8
await Order.aggregate([{$group:{_id:"$user",count:{$sum:1}}}]);
// 9  unique index (email) blocks duplicates; compound index ({user:1,createdAt:-1}) speeds a common filter+sort.
// 10  populate = normalized, no dupes, extra query. Embedding = one read, but data is duplicated & harder to update.
// 11
// SELECT u.id, COUNT(o.id) FROM users u LEFT JOIN orders o ON o.user_id=u.id GROUP BY u.id;
// 12  Fetching a list then one query per item (1 + N). Fix: JOIN / populate / WHERE id IN (...) / DataLoader.
// 13  Debit + credit must both commit or both roll back: session.startTransaction() ... commit / abort.
// 14  The driver escapes values -> blocks SQL injection; concatenation lets user input become executable code.
// 15  model User { id Int @id @default(autoincrement()) posts Post[] }
//     model Post { id Int @id @default(autoincrement()) authorId Int author User @relation(fields:[authorId],references:[id]) }
```
