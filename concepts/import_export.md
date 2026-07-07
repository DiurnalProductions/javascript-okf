---
type: Concept
title: Import and Export
description: "Static export declarations, import forms, re-exports, and live binding semantics in ES modules."
tags: [javascript, modules, import, export]
prerequisites:
  - concepts/modules
related:
  - concepts/runtime_evaluation
  - concepts/scope
resource: "https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import"
timestamp: 2026-01-01
---

## Summary

`export` exposes bindings from a module; `import` creates local bindings linked to the exporter's variables. ESM supports named exports, default exports, namespace imports, and re-exports. All static imports form a **module graph** resolved before execution.

## Mental model

Export forms:

- `export const x = 1` — named export
- `export { x, y as z }` — export list
- `export default expression` — default export (one per module)
- `export { x } from './other.js'` — re-export without local binding

Import forms:

- `import { x } from './mod.js'` — named imports
- `import def from './mod.js'` — default import
- `import * as ns from './mod.js'` — namespace object (module record snapshot for exports)
- `import './side-effects.js'` — execute module for side effects only

**Live bindings**: imported `x` is not a copied value — it references the exporter's binding. If the exporter does `export let count = 0` and later `count++`, importers reading `count` see updates (before/after evaluation depending on timing).

The module graph must be **acyclic or cycle-safe** — the linker creates uninitialized bindings for cycles and evaluates modules in dependency order, running bodies when dependencies are linked.

## Example

```javascript
// counter.js
export let count = 0;
export function increment() {
  count++;
}
```

```javascript
// app.js
import { count, increment } from "./counter.js";

increment();
console.log(count); // 1 — live binding
```

```javascript
// barrel re-export pattern
export { Button } from "./Button.js";
export { Modal } from "./Modal.js";
export { default as theme } from "./theme.js";
```

## Common mistakes

- Importing a default export with the wrong name — default import name is arbitrary but must use default import syntax.
- Expecting `import { default as x }` and `import x` to differ — they reference the same default export.
- Circular imports where initialization order leaves bindings in TDZ — design exports to avoid read-before-init.
- Renaming with `import { x as y }` but exporting with mismatched names on the other side.
- Tree-shaking failures from `export *` or side-effect imports that pull entire modules.

## Related concepts

* [ES Modules](/concepts/modules.md) — module system overview
* [Runtime Module Evaluation](/concepts/runtime_evaluation.md) — when imports resolve and bind
* [Scope](/concepts/scope.md) — module scope isolates imports/exports
