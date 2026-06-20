---
id: javascript.scope
type: concept
title: Scope
description: Lexical scope, block scope, and how the engine resolves identifiers through nested environments.
tags: [javascript, fundamentals, scope, lexical]
prerequisites:
  - javascript.variables
related:
  - javascript.hoisting
  - javascript.closures
  - javascript.functions
resource: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let#description
timestamp: 2026-01-01
---

## Summary

**Scope** determines where a binding is visible and accessible. JavaScript uses **lexical (static) scope**: a function's free variables are resolved by where the function is **written**, not where it is **called**.

## Mental model

Each running scope corresponds to an **environment record** on the scope chain. When the engine encounters an identifier, it searches the current environment, then parent environments, until it reaches the global environment or throws `ReferenceError`.

- **Block scope** (`let`, `const`): environments created by `{}`, `if`, `for`, `switch`, etc.
- **Function scope** (`var`, function declarations in non-block contexts): one environment per function body.
- **Module scope**: top-level bindings in ES modules are module-scoped, not global.
- **Global scope**: in browsers, `var` and undeclared assignments attach to the global object; `let`/`const` at top level create global bindings without becoming properties of `window`.

Scope is resolved at **parse/compile time** for lexical bindings. This is why closures work — inner functions carry a reference to their outer lexical environment.

## Example

```javascript
const global = "outer";

function outer() {
  const outerVar = "middle";

  function inner() {
    const innerVar = "inner";
    console.log(global, outerVar, innerVar); // all accessible
  }

  inner();
  // console.log(innerVar); // ReferenceError
}

outer();
```

```javascript
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 0); // 0, 1, 2 — new binding per iteration
}

for (var j = 0; j < 3; j++) {
  setTimeout(() => console.log(j), 0); // 3, 3, 3 — single function-scoped binding
}
```

## Common mistakes

- Expecting block-scoped `let`/`const` to leak outside `if` or loop blocks.
- Using `var` in loops with async callbacks and getting a shared final value.
- Assuming `this` follows scope rules — `this` is separate from lexical scope (see [This Binding](/concepts/this_binding.md)).
- Believing JavaScript has dynamic scope for variables — only `this` has dynamic binding rules.

## Related concepts

* [Variables](/concepts/variables.md) — declarations that create scoped bindings
* [Hoisting](/concepts/hoisting.md) — how declarations enter scope before execution
* [Closures](/concepts/closures.md) — functions that retain outer scope access
* [Functions](/concepts/functions.md) — each invocation creates a new function scope
