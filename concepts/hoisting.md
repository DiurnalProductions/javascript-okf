---
type: Concept
title: Hoisting
description: How declarations are registered in scope during the creation phase before code execution runs.
tags: [javascript, fundamentals, hoisting, execution]
prerequisites:
  - concepts/scope
  - concepts/variables
related:
  - concepts/functions
  - concepts/execution_context
resource: "https://developer.mozilla.org/en-US/docs/Glossary/Hoisting"
timestamp: 2026-01-01
---

## Summary

**Hoisting** is the behavior where `var` declarations and function declarations are registered in their containing scope during the **creation phase** of an execution context, before line-by-line execution. `let` and `const` are also hoisted but remain in the **Temporal Dead Zone** until initialized.

## Mental model

JavaScript execution of a scope happens in two conceptual phases:

1. **Creation phase** — the engine sets up the environment record, registers bindings, and determines `this` (for functions).
2. **Execution phase** — statements run top to bottom, assignments execute, expressions evaluate.

Hoisting is not physical code movement — it is **early binding registration**:

- `var x` → binding exists immediately with value `undefined`.
- `function foo() {}` → entire function object is created and bound before execution.
- `let`/`const` → binding exists but accessing it before the declaration line throws `ReferenceError` (TDZ).
- `class` declarations → TDZ like `let`; not usable before the declaration line.

Function expressions (`const f = function() {}`) follow the rules of their variable declaration (`let`/`const`/`var`).

## Example

```javascript
console.log(hoistedVar); // undefined — var is initialized to undefined
var hoistedVar = 5;

// console.log(hoistedLet); // ReferenceError — TDZ
let hoistedLet = 5;

hoistedFn(); // works — function declaration fully hoisted
function hoistedFn() {
  return "called";
}
```

```javascript
// var in conditionals still hoists to function/global scope
if (false) {
  var x = 1;
}
console.log(x); // undefined (not ReferenceError)
```

## Common mistakes

- Thinking `let` is not hoisted — it is hoisted, but uninitialized (TDZ).
- Calling a `const`-assigned function expression before its line — TDZ applies.
- Expecting `class` to hoist like `function` — classes throw if accessed early.
- Using `typeof undeclaredVar` → `"undefined"` but `typeof letBeforeDecl` in TDZ throws.

## Related concepts

* [Scope](/concepts/scope.md) — where hoisted bindings live
* [Variables](/concepts/variables.md) — declaration forms with different hoisting rules
* [Functions](/concepts/functions.md) — declaration vs. expression hoisting differences
* [Execution Context](/concepts/execution_context.md) — creation vs. execution phases
