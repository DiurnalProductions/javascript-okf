---
id: javascript.objects
type: concept
title: Objects
description: Plain objects, property descriptors, references, and how key-value collections behave at runtime.
tags: [javascript, objects, data-structures]
prerequisites:
  - javascript.data_types
  - javascript.variables
related:
  - javascript.prototypes
  - javascript.this_binding
resource: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object
timestamp: 2026-01-01
---

## Summary

Objects are **mutable collections of properties** — each property is a key associated with a value and metadata (writable, enumerable, configurable). Object literals `{}`, `new Object()`, and class instances are all ordinary objects with internal slots and optional prototype links.

## Mental model

At runtime, an object is a heap-allocated structure with:

- **Own properties** stored directly on the object
- **`[[Prototype]]`** — internal link to another object (or `null`) for property fallback
- **Internal methods** — `[[Get]]`, `[[Set]]`, `[[HasProperty]]`, etc.

Property access `obj.key` or `obj["key"]` uses `[[Get]]`: check own properties, then walk the prototype chain until found or `undefined`.

Property assignment uses `[[Set]]`: writes to own property or creates one (if allowed), generally does not walk the chain for assignment on non-existent keys (creates own property).

`const obj = {}` fixes the binding, not the object's contents — properties can be added, changed, or removed (unless sealed/frozen).

Modern features: shorthand methods, computed keys `[expr]`, spread `{...obj}`, optional chaining `obj?.prop`, nullish coalescing for defaults.

## Example

```javascript
const user = {
  name: "Grace",
  role: "engineer",
  greet() {
    return `Hi, I'm ${this.name}`;
  },
};

user["role"] = "lead";
console.log(user.greet()); // "Hi, I'm Grace"
```

```javascript
const original = { a: 1, nested: { x: 10 } };
const copy = { ...original }; // shallow copy
copy.nested.x = 99;
console.log(original.nested.x); // 99 — shared nested reference
```

## Common mistakes

- Assuming object spread or `Object.assign` performs deep cloning.
- Using objects as Map keys without realizing they're compared by reference.
- Mutating objects passed as function arguments when immutability was expected.
- Relying on property order for logic — integer-like keys sort numerically; others follow insertion order (with nuances).
- Confusing `hasOwnProperty` with existence anywhere on the prototype chain — use `Object.hasOwn(obj, key)` in modern code.

## Related concepts

* [Data Types](/concepts/data_types.md) — objects as reference types
* [Prototypes](/concepts/prototypes.md) — `[[Prototype]]` and property delegation
* [This Binding](/concepts/this_binding.md) — methods on objects use receiver binding
* [Inheritance Model](/concepts/inheritance_model.md) — building behavior on object structure
