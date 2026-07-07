---
type: Concept
title: Functions
description: "Function declarations, expressions, arrow functions, and how callable objects participate in execution."
tags: [javascript, functions, es6, execution]
prerequisites:
  - concepts/scope
  - concepts/hoisting
related:
  - concepts/closures
  - concepts/this_binding
  - concepts/execution_context
resource: "https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Functions"
timestamp: 2026-01-01
---

## Summary

Functions are **first-class callable objects** — they can be assigned to variables, passed as arguments, and returned from other functions. Modern JavaScript provides function declarations, function expressions, arrow functions, and methods with distinct `this` and hoisting behavior.

## Mental model

Each function invocation creates a **new execution context** with its own local environment, argument bindings, and `this` binding (except arrows, which lexically capture `this`).

- **Function declaration**: `function name() {}` — hoisted with full body in its scope.
- **Function expression**: `const fn = function() {}` — binding follows `let`/`const`/`var` rules.
- **Arrow function**: `const fn = () => {}` — no own `this`, `arguments`, `super`, or `new.target`; lexical `this` from enclosing scope.
- **Method**: shorthand `obj.method() {}` — `this` set by call site when invoked as `obj.method()`.

Functions close over their lexical environment, enabling closures. The runtime stores the function object with an internal reference to its outer environment.

Parameters are local bindings initialized with argument values (with default parameters and rest parameters in ES6+).

## Example

```javascript
function add(a, b = 0) {
  return a + b;
}

const multiply = function (a, b) {
  return a * b;
};

const divide = (a, b) => a / b;

const calculator = {
  value: 0,
  add(n) {
    this.value += n;
    return this;
  },
};
```

```javascript
function createCounter() {
  let count = 0;
  return () => ++count; // closure over count
}

const counter = createCounter();
counter(); // 1
counter(); // 2
```

## Common mistakes

- Using arrow functions as object methods when `this` must refer to the object.
- Using arrow functions as constructors — they cannot be called with `new`.
- Expecting `arguments` object inside arrow functions — use rest parameters `(...args)`.
- Creating functions inside loops with `var` and wondering why they share state.
- Assuming named function expressions are hoisted like declarations.

## Related concepts

* [Scope](/concepts/scope.md) — function bodies create function scope
* [Hoisting](/concepts/hoisting.md) — declaration vs. expression behavior
* [Closures](/concepts/closures.md) — functions retaining outer bindings
* [This Binding](/concepts/this_binding.md) — call-time `this` resolution
* [Execution Context](/concepts/execution_context.md) — per-invocation runtime frame
