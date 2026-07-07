---
type: Concept
title: ES Modules
description: "ECMAScript modules with static structure, strict mode, and isolated module scope."
tags: [javascript, modules, esm, es6]
prerequisites:
  - concepts/execution_context
related:
  - concepts/import_export
  - concepts/runtime_evaluation
  - concepts/commonjs
resource: "https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules"
timestamp: 2026-01-01
---

## Summary

**ES modules (ESM)** are the standard JavaScript module system. Each module file has its own **strict, module-scoped** top-level environment. Modules are loaded and linked statically — `import` and `export` declarations are resolved before evaluation, enabling tree shaking and cyclic dependency handling via live bindings.

## Mental model

Key properties of ESM at runtime:

- **Module scope** — top-level `let`/`const`/`function`/`class` are module-private, not global.
- **Strict mode** always enabled in modules.
- **Static imports** — `import` declarations are hoisted and parsed at load time (cannot be conditional in the same module without dynamic `import()`).
- **Live bindings** — imported bindings are references to the exporting module's bindings; if the exporter mutates a `let` export, importers see the update.
- **Single evaluation** — each module is evaluated at most once per realm; cached in the module map.
- **`import.meta`** — metadata about the current module (e.g., `import.meta.url`).

In browsers: `<script type="module">`. In Node.js: `.mjs` extension or `"type": "module"` in `package.json`.

ESM is the modern default for new JavaScript code; CommonJS remains prevalent in older Node ecosystems.

## Example

```javascript
// math.js
export const PI = 3.14159;
export function area(r) {
  return PI * r * r;
}
```

```javascript
// app.js
import { PI, area } from "./math.js";

console.log(area(2));
```

```javascript
// dynamic import — returns a Promise, usable conditionally
const module = await import("./feature.js");
module.init();
```

## Common mistakes

- Mixing `require` and `import` in the same file without understanding interop rules (Node-specific).
- Expecting `import` to run synchronously in all environments — loading is async in browsers.
- Assuming named imports are copies — they are live bindings to the exporter's variables.
- Using bare specifiers (`import from 'lodash'`) in browsers without import maps or bundlers.
- Forgetting that module top-level `this` is `undefined`.

## Related concepts

* [Import and Export](/concepts/import_export.md) — syntax and binding semantics
* [Runtime Module Evaluation](/concepts/runtime_evaluation.md) — load, link, evaluate phases
* [CommonJS](/concepts/commonjs.md) — alternative module system comparison
* [Execution Context](/concepts/execution_context.md) — module evaluation creates module context
