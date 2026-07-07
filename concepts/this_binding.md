---
type: Concept
title: This Binding
description: "How the runtime determines the value of this based on call site, not definition site."
tags: [javascript, this, functions, execution]
prerequisites:
  - concepts/functions
  - concepts/execution_context
related:
  - concepts/closures
  - concepts/objects
resource: "https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this"
timestamp: 2026-01-01
---

## Summary

`this` is a **runtime binding** set when a function is **called**, based on the call pattern. It is not determined by where the function is written (unlike lexical variables). Arrow functions do not have their own `this` — they inherit `this` from the enclosing lexical scope.

## Mental model

For ordinary functions, the engine applies `this` binding rules at **call time**:

| Call pattern | `this` value |
|---|---|
| `obj.method()` | `obj` |
| `func()` (non-strict) | global object (`window` / `globalThis`) |
| `func()` (strict) | `undefined` |
| `new Func()` | newly created object |
| `func.call(ctx, ...)` / `apply` / `bind` | explicitly set `ctx` |

Arrow functions lexically capture `this` from the surrounding execution context at **definition time** — typically the `this` of the enclosing function or module/global scope.

`this` in modules and top-level script code is `undefined` in modules; in sloppy global scripts it may be the global object.

Classes use strict mode; methods auto-bind `this` only when called as `instance.method()`. Extracting `const m = instance.method; m()` loses the receiver unless bound.

## Example

```javascript
const person = {
  name: "Ada",
  greet() {
    console.log(this.name);
  },
};

person.greet(); // "Ada"

const greet = person.greet;
greet(); // undefined (strict module) or global name (sloppy)
```

```javascript
class Counter {
  count = 0;
  increment() {
    this.count++;
  }
}

const c = new Counter();
const inc = c.increment;
// inc(); // TypeError in strict class — `this` is undefined
```

```javascript
function Timer() {
  this.seconds = 0;
  setInterval(() => {
    this.seconds++; // arrow captures Timer's `this`
  }, 1000);
}
```

## Common mistakes

- Using arrow functions as object methods when dynamic `this` is required.
- Passing callbacks without binding — `array.map(obj.method)` loses `this`.
- Assuming `this` inside a closure refers to the outer object — only lexical variables follow scope; `this` follows call rules.
- Using `this` in a module top-level expecting the global object.

## Related concepts

* [Functions](/concepts/functions.md) — declarations, arrows, and methods differ in `this` behavior
* [Execution Context](/concepts/execution_context.md) — `this` is established per context
* [Objects](/concepts/objects.md) — methods rely on receiver binding
* [Closures](/concepts/closures.md) — often combined with `bind` or arrows to preserve `this`
