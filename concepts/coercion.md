---
type: Concept
title: Coercion
description: How JavaScript converts values between types implicitly and explicitly at runtime.
tags: [javascript, fundamentals, types, coercion]
prerequisites:
  - concepts/data_types
related:
  - concepts/variables
  - concepts/promises
resource: "https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures#type_coercion"
timestamp: 2026-01-01
---

## Summary

**Coercion** is the runtime conversion of a value from one type to another. JavaScript performs coercion in comparisons, arithmetic, template literals, and logical operations. Explicit coercion uses functions like `Number()`, `String()`, and `Boolean()`.

## Mental model

Coercion is not a preprocessor step — it happens **at the point of operation** when the engine needs a specific type.

Comparison coercion follows abstract equality (`==`) rules or strict equality (`===`, no coercion). The `==` operator applies `ToPrimitive`, numeric conversion, and boolean normalization in a defined order — which produces famously surprising results.

Truthy/falsy checks use `ToBoolean`: falsy values are `false`, `0`, `-0`, `0n`, `""`, `null`, `undefined`, and `NaN`. Everything else is truthy — including `[]` and `{}`.

Promise resolution and many APIs coerce thenables and values implicitly; understanding coercion prevents subtle async bugs.

## Example

```javascript
"5" + 1;   // "51" — number coerced to string for concatenation
"5" - 1;   // 4 — string coerced to number for subtraction

Boolean("");     // false
Boolean("hello"); // true
Boolean([]);     // true — empty array is truthy

Number("42");    // 42
Number("");      // 0
Number(null);    // 0
Number(undefined); // NaN
```

```javascript
// Prefer strict equality to avoid coercion surprises
0 == false;   // true
0 === false;  // false
"" == false;  // true
null == undefined; // true (only case where == is sometimes intentional)
```

## Common mistakes

- Using `==` when `===` is intended — especially with `0`, `""`, and `false`.
- Relying on truthiness to validate numbers — `0` is falsy but may be valid data.
- Assuming `if (obj)` means the object has properties — `{}` is truthy.
- Coercing `undefined` in arithmetic without expecting `NaN`.
- Forgetting that template literals call `ToString` on embedded expressions.

## Related concepts

* [Data Types](/concepts/data_types.md) — source and target types for coercion
* [Variables](/concepts/variables.md) — bindings affected by coerced comparisons
* [Promises](/concepts/promises.md) — resolution values undergo type and thenable handling
