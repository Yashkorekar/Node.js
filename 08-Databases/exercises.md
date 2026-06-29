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
