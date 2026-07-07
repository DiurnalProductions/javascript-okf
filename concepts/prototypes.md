---
type: Concept
title: Prototypes
description: "The prototype chain, [[Prototype]] links, and how JavaScript delegates property lookup."
tags: [javascript, prototypes, object-model]
prerequisites:
  - concepts/objects
  - concepts/functions
related:
  - concepts/inheritance_model
resource: "https://developer.mozilla.org/en-US/docs/Web/JavaScript/Inheritance_and_the_prototype_chain"
timestamp: 2026-01-01
---

## Summary

Every object has an internal `[[Prototype]]` link (exposed as `__proto__` or via `Object.getPrototypeOf()`). When a property is not found on the object itself, the engine searches the **prototype chain** — a linked list of objects ending at `null`.

## Mental model

JavaScript does not copy methods into instances by default (unlike classical inheritance). Instead, instances **delegate** to a shared prototype object.

- `Object.create(proto)` creates an object whose `[[Prototype]]` is `proto`.
- Constructor functions set `Constructor.prototype` as the `[[Prototype]]` of instances created with `new Constructor()`.
- `class` syntax is primarily **syntactic sugar** over prototype wiring — methods land on the prototype object.

Functions are objects too: `Function.prototype` is the fallback for all function objects; `Object.prototype` is near the top of most chains.

Property shadowing: assigning `instance.prop = value` creates an **own** property on the instance, hiding a same-named prototype property without deleting the prototype copy.

`instanceof Constructor` checks whether `Constructor.prototype` appears anywhere in `instance`'s prototype chain.

## Example

```javascript
const animal = {
  speak() {
    return "sound";
  },
};

const dog = Object.create(animal);
dog.speak = function () {
  return "woof";
};

dog.speak(); // "woof" — own property shadows prototype
```

```javascript
function Person(name) {
  this.name = name;
}
Person.prototype.greet = function () {
  return `Hello, ${this.name}`;
};

const ada = new Person("Ada");
ada.greet(); // delegates to Person.prototype
```

## Common mistakes

- Modifying `Constructor.prototype` after creating instances — usually fine (live link), but replacing the entire prototype object breaks `instanceof` for earlier instances.
- Iterating `for...in` without filtering prototype properties — use `Object.hasOwn()`.
- Assuming `__proto__` is the standard API — prefer `Object.getPrototypeOf` / `Object.setPrototypeOf`.
- Thinking `class` eliminates prototypes — it hides them, not removes them.

## Related concepts

* [Objects](/concepts/objects.md) — own properties vs. inherited properties
* [Functions](/concepts/functions.md) — constructor functions and `.prototype` property
* [Inheritance Model](/concepts/inheritance_model.md) — patterns built on prototype delegation
