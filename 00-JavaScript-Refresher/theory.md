# 00 — JavaScript Refresher

Node.js *is* JavaScript running outside the browser. Before Node concepts, you must be fluent in modern (ES6+) JavaScript. Coming from Java this is the language part you need.

---

## 1. Variables: `let`, `const`, `var`

```js
const PI = 3.14;     // can't reassign (like final in Java)
let count = 0;       // block-scoped, reassignable (like a normal local var)
var old = "avoid";   // function-scoped, legacy — don't use
```
- **Use `const` by default**, `let` when you must reassign. Never `var`.
- JS is **dynamically typed**: a variable can hold any type. There's no `int`/`String` declaration.

**SB parallel:** `const` ≈ `final`. But there's no compile-time type checking unless you use TypeScript.

## 2. Primitive types & truthiness

```js
typeof "hi"      // "string"
typeof 42        // "number"   (no int/long/double distinction)
typeof true      // "boolean"
typeof undefined // "undefined" (declared, no value)
typeof null      // "object"   (historic bug)
typeof {}        // "object"
typeof []        // "object"   (arrays are objects)
typeof (()=>{})  // "function"
```
Falsy values: `false, 0, "", null, undefined, NaN`. Everything else is truthy.
Always use `===` / `!==` (strict, no type coercion) over `==`.

## 3. Functions

```js
function add(a, b) { return a + b; }          // declaration
const sub = function (a, b) { return a - b; }; // expression
const mul = (a, b) => a * b;                    // arrow (concise)
const greet = name => `Hello ${name}`;          // template literal
```
Arrow functions don't have their own `this` — they inherit it from the enclosing scope (important later).

## 4. Objects & destructuring

```js
const user = { name: "Asha", role: "dev", age: 30 };
const { name, role } = user;        // pull fields out
const { age: years } = user;        // rename
const merged = { ...user, age: 31 }; // spread + override
```

## 5. Arrays & functional methods

```js
const nums = [1, 2, 3, 4];
nums.map(n => n * 2);          // [2,4,6,8]   (like stream().map)
nums.filter(n => n % 2 === 0); // [2,4]       (like stream().filter)
nums.reduce((s, n) => s + n, 0); // 10        (like reduce)
nums.forEach(n => console.log(n));
const [first, ...rest] = nums;  // first=1, rest=[2,3,4]
```

## 6. Classes

```js
class Animal {
  constructor(name) { this.name = name; }
  speak() { return `${this.name} makes a sound`; }
}
class Dog extends Animal {
  speak() { return `${this.name} barks`; }
}
new Dog("Rex").speak(); // "Rex barks"
```

## 7. Template literals, optional chaining, nullish

```js
const msg = `Total: ${1 + 2}`;       // Total: 3
const city = user?.address?.city;     // undefined instead of crash
const port = process.env.PORT ?? 3000; // default if null/undefined
```

## 8. JSON

```js
const obj = JSON.parse('{"a":1}');   // string -> object
const str = JSON.stringify(obj);     // object -> string
```

---

## SB parallels cheat-sheet
| Java | JavaScript |
|---|---|
| `final` | `const` |
| `stream().map/filter/reduce` | `arr.map/filter/reduce` |
| `Optional.ofNullable(x).map(..)` | `x?.field` |
| POJO | object literal `{}` |
| `class extends` | `class extends` |
| Strong typing | dynamic (or TypeScript) |

Next: [`exercises.md`](exercises.md), then folder `01-Node-Fundamentals`.
