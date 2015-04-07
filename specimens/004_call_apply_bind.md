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
    <pre><code>
    // bind(func, thisArg) takes a function and an object and returns
    // a new function which invokes func using thisArg as the value of
    // this.
    //
    // The function returned by bind should pass arguments through to
    // func.
    function bind(func, thisArg) {
      // TODO
    }
    
    // Example:
    
    var ary = [];
    var f = bind(Array.prototype.push, ary);
    // Equivalent to calling ary.push('hello', 'world')
    f('hello', 'world');
    console.log(ary); // -> ['hello', 'world']
    </code></pre>

4. Write the body of this function:
    <pre><code>
    // bindPartial(func, thisArg, [arg1, arg2...]) takes a function,
    // an object, and any number of additional arguments. It returns
    // a new function which invokes func with the specified context
    // and initial arguments.
    function bindPartial(func, thisArg) {
      // TODO
    }
    
    // Example 1:
      
    var ary = [];
    var f = bindPartial(Array.prototype.push, ary, '>>');
    // Equivalent to calling ary.push('>>', 'hello', 'world')
    f('hello', 'world'); 
    console.log(ary); // ['>>', 'hello', 'world'];
    
    // Example 2:
    
    var max = bindPartial(Math.max, Math, 100);
    // Equivalent to calling Math.max(100, 99, 98, 19, 22)
    console.log(max(99, 98, 19, 22));  // -> 100
    console.log(max(99, 98, 192, 2));  // -> 192
    </code></pre>

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