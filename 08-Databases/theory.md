# 08 — Databases (MongoDB + SQL)

Most freelance Node jobs use MongoDB (Mongoose) or Postgres/MySQL (Prisma/Knex). Learn both.

---
## A) MongoDB + Mongoose (NoSQL, document store)
```bash
npm install mongoose
```
```js
import mongoose from "mongoose";
await mongoose.connect("mongodb://localhost:27017/shop");

const productSchema = new mongoose.Schema({
  name: { type: String, required: true },
  price: { type: Number, min: 0 },
  createdAt: { type: Date, default: Date.now }
});
const Product = mongoose.model("Product", productSchema);

// CRUD
const p = await Product.create({ name: "Pen", price: 5 });
const all = await Product.find();
const one = await Product.findById(p._id);
await Product.updateOne({ _id: p._id }, { price: 6 });
await Product.deleteOne({ _id: p._id });
const cheap = await Product.find({ price: { $lt: 10 } }).sort({ price: 1 }).limit(5);
```
**SB parallel:** Mongoose Schema/Model ≈ JPA `@Entity` + repository. Documents instead of rows.

## Relationships (reference + populate)
```js
const orderSchema = new mongoose.Schema({ user: { type: mongoose.Schema.Types.ObjectId, ref: "User" } });
const order = await Order.findById(id).populate("user"); // like JOIN fetch
```

---
## B) SQL with Prisma (typed, modern ORM)
```bash
npm install prisma @prisma/client && npx prisma init
```
```prisma
// schema.prisma
model Product { id Int @id @default(autoincrement()) name String price Float }
```
```bash
npx prisma migrate dev --name init
```
```js
import { PrismaClient } from "@prisma/client";
const db = new PrismaClient();
await db.product.create({ data: { name: "Pen", price: 5 } });
const all = await db.product.findMany({ where: { price: { lt: 10 } } });
```
**SB parallel:** Prisma ≈ JPA/Hibernate. `schema.prisma` ≈ entities; `migrate` ≈ Flyway/Liquibase.

---
## C) Connection pattern
- Connect once at startup, reuse. Never connect per request.
- Put DB URL in `.env`, never hardcode credentials.
```js
import "dotenv/config";
await mongoose.connect(process.env.MONGO_URL);
```

## D) Repository pattern
Keep DB calls in `models/`, business rules in `services/`. Controllers shouldn't touch the DB directly.

## E) Indexes & validation
- Add indexes on fields you query/sort by (huge perf win).
- Validate at the schema and at the API (Zod) layer.

## When to pick which
- Flexible/varied documents, fast prototyping → MongoDB.
- Relations, transactions, reporting → SQL (Postgres) + Prisma.

Next: `exercises.md`, then `09-Auth-and-Security`.
