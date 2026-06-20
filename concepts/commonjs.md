---
id: javascript.commonjs
type: concept
title: CommonJS
description: The require/module.exports module system used in Node.js, compared conceptually with ES modules.
tags: [javascript, modules, commonjs, node]
prerequisites:
  - javascript.modules
related:
  - javascript.import_export
  - javascript.runtime_evaluation
resource: https://nodejs.org/api/modules.html
timestamp: 2026-01-01
---

## Summary

**CommonJS (CJS)** is the legacy Node.js module system using `require()` for synchronous loading and `module.exports` / `exports` for exporting. Unlike ESM, CJS exports are **copies** of values at export time (for primitives) or **shared references** (for objects), and `require` is resolved and executed **at runtime** when the line is hit.

## Mental model

CJS module wrapper (conceptual):

```javascript
(function (exports, require, module, __filename, __dirname) {
  // module body
});
```

Key differences from ESM:

| Aspect | CommonJS | ES Modules |
|---|---|---|
| Loading | Synchronous `require()` | Static `import` / async `import()` |
| Exports | `module.exports` snapshot semantics | Live bindings for named exports |
| Scope | Function-wrapped module scope | Module environment record |
| `this` at top level | `module.exports` | `undefined` |
| Strict mode | Not default | Always strict |
| Tree shaking | Limited (dynamic structure) | Static analysis friendly |

`require` caches modules in `require.cache` — first `require` evaluates; subsequent calls return the same `module.exports` object.

Interop in Node: `import` from CJS packages often works via synthetic default export; `require` of ESM may be restricted depending on Node version and package `"type"`.

Modern recommendation: use **ESM for new code**; understand CJS when maintaining Node libraries and tooling.

## Example

```javascript
// logger.cjs
function log(msg) {
  console.log(`[LOG] ${msg}`);
}
module.exports = { log };
```

```javascript
// app.cjs
const { log } = require("./logger.cjs");
log("started");
```

```javascript
// Object exports are shared references
// counter.cjs
let count = 0;
module.exports = {
  increment() { count++; },
  getCount() { return count; },
};
```

## Common mistakes

- Mutating `exports` directly instead of `module.exports` when replacing the entire export object.
- Assuming CJS named exports are live like ESM — reassigning `count` internally is not visible unless exposed via getter/method.
- Using `require` inside ESM without `createRequire` — not available by default.
- Circular `require` where partially initialized `module.exports` is `{}` until assignment completes.
- Expecting browser-native `require` without a bundler — not part of the web platform.

## Related concepts

* [ES Modules](/concepts/modules.md) — modern standard module system
* [Import and Export](/concepts/import_export.md) — ESM binding model contrast
* [Runtime Module Evaluation](/concepts/runtime_evaluation.md) — ESM load/link/evaluate vs. CJS require cache
