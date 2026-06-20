# JavaScript Knowledge Pack

A graph-based learning module for modern JavaScript (ES6+), the runtime execution model, asynchronous behavior, and module systems.

## Start here

If you are new to JavaScript or want a solid mental model before syntax details, begin with [Variables](concepts/variables.md). JavaScript behavior is driven by how the **single-threaded runtime** evaluates code — not by memorizing APIs. Understanding scope, the call stack, and the event loop early prevents most confusion later.

Recommended first concept: [Variables](concepts/variables.md) → [Data Types](concepts/data_types.md) → [Scope](concepts/scope.md)

## Why order matters

JavaScript looks like a simple scripting language, but most surprises come from **runtime mechanics**:

- **Scope and closures** explain why inner functions retain access to outer variables.
- **Hoisting** explains why declarations seem to appear before they are written.
- **`this` binding** is determined at call time, not definition time.
- **The prototype chain** is the real inheritance system — not classes alone.
- **Async is event-loop driven** — `async/await` is syntax over Promises, not parallel threads.

Learning concepts out of order (e.g., Promises before the event loop, or classes before prototypes) leads to cargo-cult patterns and subtle bugs. The dependency graph in this pack is a **directed acyclic graph (DAG)** — follow prerequisites for the clearest path.

## Recommended learning path

### Beginner — language fundamentals

1. [Variables](concepts/variables.md)
2. [Data Types](concepts/data_types.md)
3. [Coercion](concepts/coercion.md)
4. [Scope](concepts/scope.md)
5. [Hoisting](concepts/hoisting.md)

### Intermediate — execution model

6. [Functions](concepts/functions.md)
7. [Execution Context](concepts/execution_context.md)
8. [Closures](concepts/closures.md)
9. [This Binding](concepts/this_binding.md)

### Intermediate — object model

10. [Objects](concepts/objects.md)
11. [Prototypes](concepts/prototypes.md)
12. [Inheritance Model](concepts/inheritance_model.md)

### Intermediate — async system

13. [Event Loop](concepts/event_loop.md)
14. [Promises](concepts/promises.md)
15. [Async/Await](concepts/async_await.md)

### Intermediate — modules

16. [ES Modules](concepts/modules.md)
17. [Import and Export](concepts/import_export.md)
18. [Runtime Module Evaluation](concepts/runtime_evaluation.md)
19. [CommonJS](concepts/commonjs.md) (conceptual comparison)

### Advanced — runtime behavior

20. [Memory Model Basics](concepts/memory_model.md)
21. [Garbage Collection](concepts/garbage_collection.md)
22. [Performance Considerations](concepts/performance.md)

## Concept groups

### Fundamentals

* [Variables](concepts/variables.md) — `let`, `const`, `var`, and binding rules
* [Data Types](concepts/data_types.md) — primitives, objects, and type distinctions
* [Coercion](concepts/coercion.md) — implicit and explicit type conversion
* [Scope](concepts/scope.md) — lexical scope and block scope
* [Hoisting](concepts/hoisting.md) — declaration phase vs. execution phase

### Execution model

* [Functions](concepts/functions.md) — declarations, expressions, arrow functions
* [Execution Context](concepts/execution_context.md) — call stack, environment records
* [Closures](concepts/closures.md) — functions that close over lexical environments
* [This Binding](concepts/this_binding.md) — how `this` is resolved at call time

### Object model

* [Objects](concepts/objects.md) — properties, descriptors, and object literals
* [Prototypes](concepts/prototypes.md) — `[[Prototype]]` and the prototype chain
* [Inheritance Model](concepts/inheritance_model.md) — prototypal inheritance and `class` syntax

### Async system

* [Event Loop](concepts/event_loop.md) — macrotasks, microtasks, and concurrency model
* [Promises](concepts/promises.md) — deferred results and Promise chaining
* [Async/Await](concepts/async_await.md) — syntactic sugar over Promises

### Modules

* [ES Modules](concepts/modules.md) — `import`/`export` and module scope
* [Import and Export](concepts/import_export.md) — static module graph and bindings
* [Runtime Module Evaluation](concepts/runtime_evaluation.md) — how modules are loaded and evaluated
* [CommonJS](concepts/commonjs.md) — `require`/`module.exports` and comparison with ESM

### Advanced runtime

* [Memory Model Basics](concepts/memory_model.md) — stack, heap, and references
* [Garbage Collection](concepts/garbage_collection.md) — reachability and reclamation
* [Performance Considerations](concepts/performance.md) — hot paths, allocations, and async pitfalls
