---
id: javascript.promises
type: concept
title: Promises
description: Deferred single-result values with settled states and microtask-scheduled reactions.
tags: [javascript, async, promises, es6]
prerequisites:
  - javascript.event_loop
related:
  - javascript.async_await
  - javascript.coercion
resource: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
timestamp: 2026-01-01
---

## Summary

A **Promise** represents a value (or failure) that may be available **later**. A Promise is always in one of three states: **pending**, **fulfilled**, or **rejected**. Once settled, it cannot change state. Reactions registered with `.then`, `.catch`, and `.finally` run as **microtasks** after the current synchronous code completes.

## Mental model

Promises formalize the "eventually" pattern:

- **Executor** runs synchronously when `new Promise(executor)` is called.
- `resolve` and `reject` transition the Promise from pending to settled (only the first call matters).
- `.then(onFulfilled, onRejected)` schedules handlers on settlement — not immediately.
- `.then` returns a **new Promise**, enabling chaining. Return values fulfill; thrown errors reject; returned Promises adopt their state.

`Promise.resolve(x)` and `Promise.reject(err)` create already-settled promises. `await` is built on this adoption mechanism.

Unhandled rejections occur when a Promise rejects with no rejection handler attached by the time it settles — environments may log warnings or crash (Node.js).

`Promise.all`, `race`, `allSettled`, and `any` compose multiple promises with different settlement rules.

## Example

```javascript
function delay(ms) {
  return new Promise((resolve) => setTimeout(resolve, ms));
}

delay(100)
  .then(() => "done")
  .then((value) => console.log(value))
  .catch((err) => console.error(err));
```

```javascript
Promise.all([fetch("/a"), fetch("/b")])
  .then(([resA, resB]) => Promise.all([resA.json(), resB.json()]))
  .then(([dataA, dataB]) => {
    // both succeeded
  });
```

## Common mistakes

- Creating the "Promise constructor antipattern" — wrapping APIs that already return Promises.
- Forgetting to `return` inside `.then` chains, breaking downstream data flow.
- Swallowing errors with `.catch(() => {})` without rethrowing or handling.
- Assuming `.then(fn)` runs before the next synchronous line — it is always async (microtask).
- Mixing `async` functions that throw with `.then` chains without understanding both reject the Promise.
- Using `Promise.all` when partial failure should be tolerated — consider `allSettled`.

## Related concepts

* [Event Loop](/concepts/event_loop.md) — Promise reactions are microtasks
* [Async/Await](/concepts/async_await.md) — ergonomic syntax over Promises
* [Coercion](/concepts/coercion.md) — non-Promise return values are wrapped with `Promise.resolve`
