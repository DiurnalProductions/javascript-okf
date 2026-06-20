---
id: javascript.data_types
type: concept
title: Data Types
description: Primitive and object types in JavaScript, including typeof behavior and value vs. reference semantics.
tags: [javascript, fundamentals, types, primitives]
prerequisites:
  - javascript.variables
related:
  - javascript.coercion
  - javascript.objects
resource: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures
timestamp: 2026-01-01
---

## Summary

JavaScript has **seven primitive types** (`string`, `number`, `bigint`, `boolean`, `undefined`, `symbol`, `null`) and **objects** (including arrays, functions, dates, and plain objects). Primitives are immutable values; objects are mutable references stored on the heap.

## Mental model

The runtime distinguishes **value types** (primitives, copied by value) from **reference types** (objects, copied by reference — the reference itself is a value, often described as "copy by sharing").

- Primitives live as atomic values. Operations create new values rather than mutating existing ones (`"ab"` is a new string).
- Objects are collections of properties identified by a reference. Multiple bindings can point to the same object.
- `typeof null === "object"` is a long-standing language quirk — treat `null` as its own nullish type in practice.
- Functions are callable objects with an internal `[[Call]]` method.

Type checks at runtime use `typeof`, `instanceof`, `Array.isArray()`, and `Object.prototype.toString.call()` depending on precision needed.

## Example

```javascript
let a = { x: 1 };
let b = a;
b.x = 2;
console.log(a.x); // 2 — same object reference

let s1 = "hello";
let s2 = s1;
s2 = s2 + "!";
console.log(s1); // "hello" — primitives are immutable, s2 points to a new string
```

```javascript
typeof 42;           // "number"
typeof "text";       // "string"
typeof true;         // "boolean"
typeof undefined;    // "undefined"
typeof Symbol();     // "symbol"
typeof 10n;          // "bigint"
typeof null;         // "object" (historical bug)
typeof {};           // "object"
typeof function(){}; // "function"
```

## Common mistakes

- Using `typeof` alone to detect `null` or arrays — use `value === null` and `Array.isArray()`.
- Expecting primitive wrapper objects (`new String("a")`) to behave like primitives in `===` comparisons.
- Confusing `undefined` (declared but uninitialized / missing property) with `null` (intentional absence).
- Assuming `NaN === NaN` — use `Number.isNaN()` instead.

## Related concepts

* [Variables](/concepts/variables.md) — bindings that hold typed values
* [Coercion](/concepts/coercion.md) — automatic conversion between types
* [Objects](/concepts/objects.md) — the object type in depth
