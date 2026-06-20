---
id: javascript.memory_model
type: concept
title: Memory Model Basics
description: Stack vs. heap storage, references, bindings, and how values live in the JavaScript runtime.
tags: [javascript, memory, runtime, performance]
prerequisites:
  - javascript.execution_context
  - javascript.closures
related:
  - javascript.garbage_collection
  - javascript.data_types
resource: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Memory_management
timestamp: 2026-01-01
---

## Summary

JavaScript memory is conceptually split into a **call stack** (execution contexts, primitive values, references) and a **heap** (objects, closures' captured environments, module records). Variables hold either primitive values directly or **references** to heap objects.

## Mental model

**Stack** (per thread):

- Execution contexts pushed/popped with function calls.
- Stores references to heap objects and primitive values for active bindings.
- Limited size — stack overflow on deep recursion.

**Heap**:

- Objects, arrays, functions, closures, Promise internals, module state.
- Allocated dynamically; lifetime not tied to a single stack frame.

**Bindings** in environment records either contain a primitive value or a reference (pointer) to a heap object. Assignment `a = b` for objects copies the reference — both bindings point to the same heap object.

Closures keep environment records alive on the heap even after the outer stack frame is popped, because inner functions hold `[[Environment]]` references.

Primitives are generally stored by value in bindings; engines may optimize (string interning, small integer caching) but the mental model remains value vs. reference.

TypedArrays and ArrayBuffers expose raw binary memory — relevant for WASM, media, and performance-critical code.

## Example

```javascript
function createItems() {
  const items = []; // heap array
  for (let i = 0; i < 1000; i++) {
    items.push({ id: i }); // each object on heap
  }
  return items; // reference escapes stack when function returns
}

const data = createItems();
// `items` stack binding is gone, but array + objects remain reachable via `data`
```

```javascript
let a = { x: 1 };
let b = a; // copies reference, not object
b.x = 2;
console.log(a.x); // 2
```

## Common mistakes

- Assuming assignment clones objects — need structured clone, spread, or serialization for copies.
- Retaining large graphs accidentally via global variables or long-lived closures.
- Confusing stack overflow with heap out-of-memory — different limits and causes.
- Creating huge monolithic arrays/objects without considering allocation churn on the main thread.

## Related concepts

* [Execution Context](/concepts/execution_context.md) — stack frames and environments
* [Closures](/concepts/closures.md) — environments retained on the heap
* [Data Types](/concepts/data_types.md) — primitives vs. references
* [Garbage Collection](/concepts/garbage_collection.md) — reclaiming unreachable heap memory
