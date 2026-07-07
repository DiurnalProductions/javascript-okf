---
type: Concept
title: Event Loop
description: "How JavaScript schedules asynchronous work on a single thread using the call stack, task queues, and microtasks."
tags: [javascript, async, event-loop, concurrency]
prerequisites:
  - concepts/execution_context
related:
  - concepts/promises
  - concepts/async_await
  - concepts/performance
resource: "https://developer.mozilla.org/en-US/docs/Web/JavaScript/Event_loop"
timestamp: 2026-01-01
---

## Summary

JavaScript is **single-threaded** — one call stack runs synchronous code at a time. **Asynchrony** comes from the **event loop**, which schedules callbacks after I/O, timers, and Promise reactions complete. The runtime interleaves macrotasks and microtasks between stack-draining cycles.

## Mental model

Components (conceptual):

- **Call stack** — currently executing synchronous execution contexts.
- **Web APIs / host APIs** — timers, network, DOM events (provided by the environment, not the language spec alone).
- **Macrotask queue** (task queue) — `setTimeout`, `setInterval`, I/O callbacks, UI events.
- **Microtask queue** — Promise `.then` / `await` continuations, `queueMicrotask`, `MutationObserver`.

Event loop algorithm (simplified):

1. Run synchronous code until the call stack is empty.
2. Drain **all** microtasks (may enqueue more microtasks — risk of starvation if infinite).
3. Render (in browsers, if applicable).
4. Take **one** macrotask, run it, return to step 1.

This means Promise callbacks run **before** the next `setTimeout(0)` — microtasks have higher priority than macrotasks.

Async does **not** mean parallel threads in standard JS — it means **deferred execution** on the same thread, interleaved via the event loop.

## Example

```javascript
console.log("1");

setTimeout(() => console.log("2"), 0);

Promise.resolve().then(() => console.log("3"));

console.log("4");

// Output: 1, 4, 3, 2
// Sync first, then microtasks, then macrotasks
```

```javascript
async function fetchData() {
  const response = await fetch("/api"); // yields to event loop while waiting
  return response.json(); // continuation is a microtask
}
```

## Common mistakes

- Assuming `setTimeout(fn, 0)` runs immediately — it runs after current sync code and microtasks.
- Blocking the main thread with heavy sync work — freezes UI and delays all async callbacks.
- Creating microtask loops that never yield — starves rendering and macrotasks.
- Thinking `async/await` runs code on another thread — it only suspends and resumes via Promises.
- Expecting order across unrelated async sources without explicit coordination.

## Related concepts

* [Execution Context](/concepts/execution_context.md) — async callbacks get new contexts later
* [Promises](/concepts/promises.md) — microtask-scheduled reactions
* [Async/Await](/concepts/async_await.md) — syntax that compiles to Promise microtasks
* [Performance Considerations](/concepts/performance.md) — main-thread blocking and scheduling
