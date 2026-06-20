---
id: javascript.garbage_collection
type: concept
title: Garbage Collection
description: How JavaScript runtimes reclaim heap memory when objects are no longer reachable.
tags: [javascript, memory, gc, runtime]
prerequisites:
  - javascript.memory_model
related:
  - javascript.closures
  - javascript.performance
resource: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Memory_management
timestamp: 2026-01-01
---

## Summary

JavaScript uses **automatic garbage collection** — developers do not `free()` memory manually. The GC identifies **reachable** objects from roots (global object, call stack bindings, registered handles) and reclaims objects that are unreachable.

## Mental model

Common algorithm family: **mark-and-sweep** (with generational and incremental optimizations in V8, SpiderMonkey, JavaScriptCore).

1. **Mark** — traverse from GC roots, mark all reachable objects.
2. **Sweep** — reclaim unmarked objects' memory.

An object is collectible when **no chain of references** exists from a root to it.

Reachability pitfalls:

- **Closures** keep outer environments alive if inner functions are reachable.
- **Event listeners** keep handler closures and captured DOM nodes alive until removed.
- **Timers** (`setInterval`) keep callbacks and closed-over state alive until cleared.
- **Global variables** and module-level caches extend lifetime for the process duration.

`WeakMap` and `WeakSet` hold **weak** references — keys/objects can be collected when no other strong references exist. `WeakRef` and `FinalizationRegistry` exist for advanced cache patterns (use sparingly).

GC runs are **non-deterministic** — pauses depend on heap size and engine heuristics. Large object graphs increase mark phase cost.

## Example

```javascript
function leakCandidate() {
  const huge = new Array(1_000_000).fill("data");
  return function () {
    // If this function is stored globally, `huge` stays reachable
    return huge.length;
  };
}

const fn = leakCandidate();
// `huge` cannot be collected while `fn` is reachable
fn = null; // if nothing else references fn, huge becomes collectible
```

```javascript
const cache = new WeakMap();

function attachMetadata(domNode, meta) {
  cache.set(domNode, meta);
  // When domNode is removed from DOM and no other refs exist, entry can be collected
}
```

## Common mistakes

- Assuming setting a variable to `null` immediately frees memory — only removes one reference; GC decides timing.
- Detached DOM nodes still reachable via JS references — classic browser memory leak.
- Global arrays that accumulate data without eviction policy.
- Closure-heavy architectures retaining entire scopes when only one small value was needed — extract primitives or use weak structures.
- Fighting GC with manual patterns from C++ instead of reducing retention.

## Related concepts

* [Memory Model](/concepts/memory_model.md) — what lives on the heap and how references work
* [Closures](/concepts/closures.md) — common source of unintended retention
* [Performance Considerations](/concepts/performance.md) — GC pause impact on responsiveness
