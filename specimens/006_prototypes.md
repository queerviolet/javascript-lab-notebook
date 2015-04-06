---
title: Specimen 006 — Prototypes
layout: specimen
category: specimen
permalink: /006_prototypes/
prev: /005_scope_2/
next: /
---

# Specimen 006: Prototypes #

    var super = {};
    var sub = Object.create(super);

    super.magical = true;

1. What is the value of the expression `sub.magical`?
2. Does the `sub` object itself have a `"magical"` key? (resources:
   [in](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/in),
   [Object.hasOwnProperty](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/hasOwnProperty),
   [Object.keys](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/keys))
3. Write a function that will make this code work:

```javascript
[1, 2, 4, 5, 6, 7].count(function(e) {
  return e % 2 === 0;
});  // -> 3
```


<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Lab notes ##

This specimen has not yet been analyzed.

