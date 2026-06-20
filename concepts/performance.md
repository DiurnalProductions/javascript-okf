---
id: javascript.performance
type: concept
title: Performance Considerations
description: Main-thread constraints, allocation patterns, async scheduling, and practical JavaScript performance habits.
tags: [javascript, performance, optimization, async]
prerequisites:
  - javascript.event_loop
  - javascript.garbage_collection
  - javascript.memory_model
related:
  - javascript.promises
  - javascript.closures
resource: https://developer.mozilla.org/en-US/docs/Web/Performance
timestamp: 2026-01-01
---

## Summary

JavaScript performance is dominated by **main-thread execution time**, **memory allocation rate**, and **async scheduling** — not raw multi-threaded CPU parallelism in typical app code. Optimizations target reducing work per frame, avoiding unnecessary allocations, and structuring async flow without blocking the event loop.

## Mental model

The runtime is single-threaded for JS execution:

- Long synchronous loops block rendering, input, and pending microtasks/macrotasks.
- Each object/array creation allocates on the heap → eventual GC cost.
- Property access on prototypes is slightly slower than own properties (often negligible; optimize only when profiled).
- `async` improves **responsiveness**, not CPU throughput — parallel CPU work needs Web Workers, WASM threads, or native addons.

Practical priorities:

1. **Measure** — profiler, Performance panel, `performance.now()`.
2. **Reduce main-thread work** — chunk with `queueMicrotask`, `requestAnimationFrame`, `setTimeout(0)`, or Workers.
3. **Cut allocations** in hot paths — reuse buffers, avoid map/filter chains in tight loops if profiling shows pressure.
4. **Batch DOM reads/writes** — layout thrashing from interleaved read/write causes reflows.
5. **Parallelize independent I/O** with `Promise.all`, not sequential `await`.

De-optimization triggers (engine-dependent): excessive object shape changes, arguments object misuse, eval, with statement (legacy).

## Example

```javascript
// Bad — blocks event loop
function processMillion(items) {
  for (let i = 0; i < items.length; i++) {
    heavySyncWork(items[i]);
  }
}

// Better — yield between chunks
async function processChunked(items, chunkSize = 100) {
  for (let i = 0; i < items.length; i += chunkSize) {
    const chunk = items.slice(i, i + chunkSize);
    for (const item of chunk) {
      heavySyncWork(item);
    }
    await new Promise((r) => setTimeout(r, 0)); // macrotask yield
  }
}
```

```javascript
// Parallel network — not parallel threads, but overlapping I/O wait
const results = await Promise.all(urls.map((url) => fetch(url)));
```

## Common mistakes

- Premature micro-optimization before profiling.
- `await` in a loop for independent async operations — unnecessary serialization.
- Creating new functions/objects inside render loops (React, games) without memoization when profiling shows cost.
- Assuming Web Workers share memory by default — use `SharedArrayBuffer` only when needed (with security constraints).
- Synchronous localStorage/JSON.parse on large payloads on the critical path.
- Ignoring microtask starvation from long Promise reaction chains.

## Related concepts

* [Event Loop](/concepts/event_loop.md) — scheduling and main-thread interleaving
* [Memory Model](/concepts/memory_model.md) — allocation and reference costs
* [Garbage Collection](/concepts/garbage_collection.md) — allocation-driven pause risk
* [Promises](/concepts/promises.md) — composition patterns for parallel I/O
* [Closures](/concepts/closures.md) — retention and allocation in callbacks
