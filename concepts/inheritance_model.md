---
type: Concept
title: Inheritance Model
description: "Prototypal inheritance, class syntax, extends, and how behavior is shared in JavaScript."
tags: [javascript, inheritance, class, prototypes]
prerequisites:
  - concepts/prototypes
related:
  - concepts/objects
  - concepts/this_binding
resource: "https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes"
timestamp: 2026-01-01
---

## Summary

JavaScript uses **prototypal inheritance**: objects inherit behavior by linking to other objects via `[[Prototype]]`. ES6 `class` syntax provides familiar structure but compiles to prototype-based wiring with `extends`, `super`, and constructor methods.

## Mental model

Inheritance in JavaScript is **delegation**, not copying:

1. An instance links to a prototype object (e.g., `SubClass.prototype`).
2. That prototype may link to another prototype (`SuperClass.prototype`) via `extends`.
3. Method lookup walks the chain; `this` inside a method refers to the original receiver.

`class Sub extends Super`:

- Sets `Sub.prototype.[[Prototype]]` to `Super.prototype`
- `super()` in constructor calls `Super` as constructor with `this` bound to the new instance
- `super.method()` starts lookup on the parent prototype but keeps child `this`

There is no multiple inheritance of classes. Mixins copy properties onto prototypes; composition is often preferred over deep hierarchies.

`static` methods live on the constructor function object, not the instance prototype chain.

## Example

```javascript
class Animal {
  constructor(name) {
    this.name = name;
  }
  speak() {
    return `${this.name} makes a sound`;
  }
}

class Dog extends Animal {
  speak() {
    return `${super.speak()} — actually, woof!`;
  }
}

const d = new Dog("Rex");
d.speak(); // "Rex makes a sound — actually, woof!"
```

```javascript
// Prototypal style (equivalent idea, no class sugar)
function Animal(name) {
  this.name = name;
}
Animal.prototype.speak = function () {
  return this.name;
};
```

## Common mistakes

- Calling `super.method()` before `super()` in a derived constructor — `this` is uninitialized until `super()` returns.
- Assuming `class` copies parent fields into child instances — only what constructors assign becomes own properties.
- Deep inheritance trees in a language designed for composition and delegation.
- Overriding methods and breaking `instanceof` by replacing prototypes incorrectly.
- Using arrow functions as class methods when subclasses need to call `super` on the method.

## Related concepts

* [Prototypes](/concepts/prototypes.md) — the underlying delegation mechanism
* [Objects](/concepts/objects.md) — instances and their own properties
* [This Binding](/concepts/this_binding.md) — `super` calls preserve receiver `this`
* [Functions](/concepts/functions.md) — constructors and class constructors
