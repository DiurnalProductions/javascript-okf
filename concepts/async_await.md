---
id: javascript.async_await
type: concept
title: Async/Await
description: Syntactic sugar for writing Promise-based asynchronous code with suspend and resume semantics.
tags: [javascript, async, await, es2017]
prerequisites:
  - javascript.promises
related:
  - javascript.event_loop
  - javascript.promises
resource: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function
timestamp: 2026-01-01
---

## Summary

`async` functions always return a **Promise**. `await` pauses execution of the `async` function until a Promise settles, then resumes with the fulfilled value or throws on rejection. Under the hood, `async/await` desugars to Promise chains and microtask-scheduled continuations.

## Mental model

An `async function` body runs synchronously until the first `await`:

1. Code before `await` runs on the call stack like normal synchronous code.
2. At `await promise`, the function **yields** — it returns control to the caller; the caller receives a Promise.
3. When `promise` settles, the remainder of the function is scheduled as a **microtask** continuation.
4. `return value` in an async function is equivalent to `Promise.resolve(value)`.
5. `throw error` rejects the returned Promise.

`await` works on thenables, not only native Promises — they are adopted via `Promise.resolve`.

Sequential `await` in a loop runs operations one after another (good for dependencies, bad for independent parallel work — use `Promise.all`).

`async/await` does not create parallelism — it structures **non-blocking** sequential async flow on a single thread.

## Example

```javascript
async function loadUser(id) {
  const response = await fetch(`/api/users/${id}`);
  if (!response.ok) {
    throw new Error(`HTTP ${response.status}`);
  }
  return response.json();
}

loadUser(1)
  .then((user) => console.log(user.name))
  .catch((err) => console.error(err));
```

```javascript
// Parallel — start both, await together
async function loadDashboard() {
  const [user, posts] = await Promise.all([
    fetch("/api/user").then((r) => r.json()),
    fetch("/api/posts").then((r) => r.json()),
  ]);
  return { user, posts };
}
```

## Common mistakes

- Using `await` in a loop for independent requests — serializes work unnecessarily.
- Forgetting that `async` functions return Promises — callers must `await` or `.then()`.
- Wrapping everything in try/catch but not awaiting inside the try block correctly.
- Assuming `await` blocks the entire program — only the current async function suspends.
- Using `async` on functions that do no async work — adds unnecessary Promise wrapping overhead.
- Top-level `await` only in modules — not in classic scripts without bundler support.

## Related concepts

* [Promises](/concepts/promises.md) — underlying mechanism for async/await
* [Event Loop](/concepts/event_loop.md) — await continuations are microtasks
* [ES Modules](/concepts/modules.md) — top-level await requires module context
