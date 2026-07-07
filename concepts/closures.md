---
type: Concept
title: Closures
description: Functions that retain access to variables from their enclosing lexical scope after the outer function returns.
tags: [javascript, closures, scope, functions]
prerequisites:
  - concepts/functions
  - concepts/scope
  - concepts/execution_context
related:
  - concepts/memory_model
  - concepts/this_binding
resource: "https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures"
timestamp: 2026-01-01
---

## Summary

A **closure** is created when a function is defined inside another function and the inner function references variables from the outer scope. The inner function carries a reference to the outer **lexical environment**, keeping those bindings alive after the outer function's execution context is popped from the call stack.

## Mental model

Closures are not a special syntax — they are the natural result of **lexical scoping** plus first-class functions.

When the engine creates a function object, it stores:

- The function's code
- A `[[Environment]]` internal slot pointing to the lexical environment where the function was **defined**

When the inner function runs (even after the outer returns), variable lookup walks that captured environment chain. This is how most JavaScript "magic" works: modules, private state, callbacks, and partial application.

Closures explain:

- Why loop callbacks see the "wrong" `var` value (one shared binding) vs. correct `let` value (per-iteration binding).
- How factory functions encapsulate state without classes.
- Why retained callbacks can prevent garbage collection of large outer scopes.

## Example

```javascript
function makeAdder(base) {
  return function (n) {
    return base + n; // closes over `base`
  };
}

const add10 = makeAdder(10);
add10(5); // 15 — `base` still accessible
```

```javascript
function createWallet(initial) {
  let balance = initial;
  return {
    deposit(amount) { balance += amount; },
    getBalance() { return balance; },
  };
}

const wallet = createWallet(100);
wallet.deposit(50);
wallet.getBalance(); // 150 — `balance` is private via closure
```

## Common mistakes

- Creating closures in loops with `var` and expecting per-iteration values — use `let` or an IIFE/factory.
- Accidentally closing over a mutable loop variable in async handlers.
- Assuming closures copy values — they capture **bindings**; if the binding's value mutates, the closure sees the mutation.
- Holding closures that reference large objects longer than needed, causing memory retention.

## Related concepts

* [Functions](/concepts/functions.md) — closures are function objects with captured environments
* [Scope](/concepts/scope.md) — lexical scope enables closure capture
* [Execution Context](/concepts/execution_context.md) — environments persist beyond context lifetime
* [Memory Model](/concepts/memory_model.md) — how captured references affect retention
