---
title: Specimen 004 — Call, apply, and bind
layout: specimen
category: specimen
permalink: /004_call_apply_bind/
prev: /003_scope/
next: /005_scope_2/
---

# Specimen 004: Call, apply, and bind #

    function toAry() {
      return Array.prototype.slice.apply(arguments);
    }

Resources: [Function.apply](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)

1. What is `Array.prototype.slice`? Is it the same as `[].slice`?

2. What does `toAry` do?

3. Write the body of this function:

```
    // Bind takes func (a function) and thisArg and returns
    // a new function which invokes `func` with the object `thisArg`
    // provided as `this`
    function bind(func, thisArg) {
      // TODO
    }
  
    Example:
  
    var ary = [];
    var f = bind(Array.prototype.push, ary);
    f('hello', 'world');
    console.log(ary); // -> ['hello', 'world']
```

4. Add support for binding additional arguments to your `bind` function.

```
    // Example:
  
    var ary = [];
    var f = bind(Array.prototype.push, ary, '>>');
    f('hello', 'world');
    console.log(ary); // ['>>', 'hello', 'world'];
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