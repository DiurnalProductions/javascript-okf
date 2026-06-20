---
id: javascript.runtime_evaluation
type: concept
title: Runtime Module Evaluation
description: How the JavaScript engine loads, links, and evaluates modules in dependency order.
tags: [javascript, modules, runtime, evaluation]
prerequisites:
  - javascript.import_export
  - javascript.modules
related:
  - javascript.execution_context
  - javascript.hoisting
resource: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules#applying_the_module_to_your_html
timestamp: 2026-01-01
---

## Summary

Module loading follows **Load → Link → Evaluate** phases. The engine builds a module graph from static `import`/`export`, resolves specifiers to URLs (host-dependent), creates live bindings between modules, then evaluates module bodies in **dependency-first** order, caching each module's namespace after first evaluation.

## Mental model

**Load phase** (host):

- Fetch module records (files) and parse into `ModuleRecord` structures.
- Resolve relative and bare specifiers to canonical URLs/paths.

**Link phase** (engine):

- Instantiate each module's environment.
- Create import/export binding connections (live links).
- Handle circular dependencies by creating bindings first, evaluating later.

**Evaluate phase** (engine):

- Run module top-level code depth-first following dependencies.
- Each module runs **once** — subsequent imports reuse the cached module namespace.
- Top-level `await` pauses evaluation of that module and unblocks dependent modules per spec rules.

Compared to scripts:

- Classic scripts share global scope and can be injected dynamically.
- Modules are deferred by default in browsers (`<script type="module" defer>` behavior).
- Dynamic `import()` performs load/link/evaluate on demand, returning a Promise.

Side effects at module top level (I/O, global mutation) run at import time — reason to prefer explicit `init()` functions for controllable startup.

## Example

```javascript
// a.js
import { bValue } from "./b.js";
export const aValue = "a";
console.log("eval a", bValue);

// b.js
import { aValue } from "./a.js";
export const bValue = "b";
console.log("eval b", aValue);

// Circular: linking succeeds; logs may show `undefined` for not-yet-evaluated exports
// depending on evaluation order within the cycle
```

```javascript
// entry.js — dynamic import loads subgraph on demand
button.addEventListener("click", async () => {
  const { heavyFeature } = await import("./heavy-feature.js");
  heavyFeature.run();
});
```

## Common mistakes

- Relying on import order side effects across many modules — implicit initialization order is fragile.
- Circular dependencies where modules read each other's exports during top-level evaluation.
- Assuming dynamic `import()` is synchronous — always returns a Promise.
- Re-importing for "fresh state" — module cache prevents re-evaluation; use factory functions instead.
- Top-level await blocking module graph completion in ways that delay app startup unexpectedly.

## Related concepts

* [ES Modules](/concepts/modules.md) — module system fundamentals
* [Import and Export](/concepts/import_export.md) — static graph edges
* [Execution Context](/concepts/execution_context.md) — module evaluation context
* [Hoisting](/concepts/hoisting.md) — import declarations are hoisted in module scope
