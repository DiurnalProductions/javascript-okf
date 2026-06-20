---
id: javascript.variables
type: concept
title: Variables
description: Declaring and binding names to values using let, const, and var in modern JavaScript.
tags: [javascript, fundamentals, es6, bindings]
prerequisites: []
related:
  - javascript.scope
  - javascript.data_types
  - javascript.hoisting
resource: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let
timestamp: 2026-01-01
---

## Summary

Variables are **bindings** between an identifier and a value in a lexical environment. Modern JavaScript uses `let` and `const` for block-scoped bindings and reserves `var` for function-scoped legacy code. `const` prevents reassignment of the binding, not mutation of object contents.

## Mental model

At runtime, declaring a variable does not "create a box" globally — it creates an entry in the **current environment record** (scope). The engine resolves identifiers by walking the scope chain from inner to outer environments.

- `let` and `const` are **block-scoped** and live in the Temporal Dead Zone (TDZ) until their declaration line executes.
- `var` is **function-scoped** (or global-scoped) and is initialized with `undefined` during the creation phase of its scope.
- Reassignment changes which value a binding points to; it does not change the binding's scope.

JavaScript is single-threaded: variable bindings are mutated synchronously on one thread. There is no shared-memory concurrency at the language level for plain variables.

## Example

```javascript
const user = { name: "Ada" };
user.name = "Grace"; // OK — mutating the object, not reassigning the binding

let count = 0;
count = 1; // OK — let allows reassignment

// const count = 2; // SyntaxError — cannot reassign const binding
```

```javascript
{
  let blockScoped = true;
}
// console.log(blockScoped); // ReferenceError — binding does not exist outside the block
```

## Common mistakes

- Assuming `const` makes objects immutable — only the **binding** is fixed.
- Using `var` in loops and expecting each iteration to have its own binding (use `let`).
- Accessing `let`/`const` before declaration — TDZ throws `ReferenceError`, not `undefined`.
- Treating undeclared assignment (`x = 1`) as a variable declaration — it creates or assigns a global property in non-strict sloppy mode, which is almost always unintended.

## Related concepts

* [Scope](/concepts/scope.md) — where bindings are visible
* [Data Types](/concepts/data_types.md) — what values bindings hold
* [Hoisting](/concepts/hoisting.md) — when bindings become available during execution
