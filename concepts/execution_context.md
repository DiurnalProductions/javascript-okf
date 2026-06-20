---
id: javascript.execution_context
type: concept
title: Execution Context
description: The runtime frame for executing code, including the call stack, environment records, and this binding.
tags: [javascript, execution, runtime, call-stack]
prerequisites:
  - javascript.functions
  - javascript.scope
related:
  - javascript.closures
  - javascript.this_binding
  - javascript.event_loop
resource: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Scopes
timestamp: 2026-01-01
---

## Summary

An **execution context** is the runtime environment in which JavaScript code is evaluated. Each function call, global script, and module evaluation creates a context with its own variable environment, lexical environment, and `this` binding. Contexts are stacked on the **call stack**.

## Mental model

JavaScript runs on a **single call stack** — only one execution context executes synchronously at a time.

When code runs:

1. A **global execution context** is created when a script or module starts.
2. Each **function invocation** pushes a new execution context onto the call stack.
3. When a function returns, its context is **popped** from the stack.

Each context contains:

- **Lexical Environment** — bindings for `let`/`const`/class and outer environment reference.
- **Variable Environment** — bindings for `var` (historically separate; often merged conceptually).
- **`this` binding** — determined by how the function was called (except arrows).
- **Code evaluation state** — where execution paused (for generators/async).

The engine resolves variables by walking the lexical environment chain. This chain is what closures preserve after outer functions return.

Async callbacks run later in **new** execution contexts, scheduled by the event loop — not as continuations on the same stack frame.

## Example

```javascript
function first() {
  const a = 1;
  second();
  return a;
}

function second() {
  const b = 2;
  // call stack: second → first → global
}

first();
// after return, second and first contexts are popped; only global remains
```

```javascript
let x = "global";

function outer() {
  let x = "outer";
  function inner() {
    console.log(x); // "outer" — walks lexical environment chain
  }
  return inner;
}

const fn = outer();
fn(); // still "outer" — inner's environment references outer's lexical env
```

## Common mistakes

- Assuming async code continues on the same stack frame — it runs in a later turn with a fresh context.
- Confusing execution context with scope — scope is lexical structure; context is the runtime instance of evaluation.
- Expecting deep recursion without limit — stack overflow occurs when the call stack exceeds engine limits.
- Believing each `setTimeout` callback shares the caller's local variables unless closed over via closure.

## Related concepts

* [Functions](/concepts/functions.md) — each call creates a new context
* [Scope](/concepts/scope.md) — static structure that contexts instantiate
* [Closures](/concepts/closures.md) — retained lexical environments after context pop
* [This Binding](/concepts/this_binding.md) — part of each function context
* [Event Loop](/concepts/event_loop.md) — schedules new contexts for async work
