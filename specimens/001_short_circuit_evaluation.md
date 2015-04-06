---
title: Specimen 001 — Short circuit evaluation
layout: specimen
category: specimen
permalink: 001_short_circuit_evaluation/
prev: /
next: /002_callbacks_and_asynchrony/
---

# Specimen 001: Short circuit evaluation #

*Research: You may find the [first chapter](http://speakingjs.com/es5/ch01.html) of Speaking Javascript, specifically the section on
[binary logical operators](http://speakingjs.com/es5/ch01.html#_binary_logical_operators),
helpful in describing the behavior you see here.*

    var f1 = function() {
      return 'mimas';
    };
  
    var f2 = function() {
      console.log('enceladus');
    };
  
    var x = false || 'tethys';
    var y = 'dione' || 'rhea';
    var y = y || {};
    var z = z || {};
    var a = a || f2() || f1();

1. What is the value of the expression `false || 'tethys'`?
2. What is the value of the expression `'dione' || 'rhea'`?
3. After the second assignment of `y`, `var y = y || {};`, has `y` changed? Why
   not?
4. What does `var z = z || {};` do?
5. What does `f2` return? (That is, what is the value of the expression
   `f2()`?)
6. What does `var a = a || f2() || f1();` print, and what is the value of a
   afterwards?  What would happen if we swapped the calls to `f2` and `f1`?

Explain what's going on here. How does Javascript evaluate boolean expressions
like `x && y or false || f() || true`?

Finally, imagine I asked you to write an `or` function, which functions exactly
like the || operator.

    // or(x,y, z) is identical to typing x || y || z
    function or() {
      // TODO
    }

Explain why this is impossible (and, if you like, how you could achieve something similar).

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

## Lab notes ##

*__What is the value of the expression `false || 'tethys'`?__*

    > false || 'tethys'
    'tethys'

I'm starting to form a hypothesis: In Javascript, the expression `x || y` has
the value of `x` if `x` is truthy, or `y` if `x` is falsy and `y` is truthy.

I don't know what the value of `x || y` is if `x` and `y` are both falsey.
(Note: as I'm writing this, I'm actually not sure. I'm reasonably sure it's
`false`, but let's find out!) First, let me do a quick experiment to get a
better idea of what Javascript considers truthy (true in a boolean context)
and falsey (false in a boolean context), since I'm not sure:

    > function truthiness(value) {
        if (!value) {
            return 'falsey';
        } else {
            return 'truthy';
        }
    }

    > truthiness(1);
    'truthy'

I started with `1` to test my experiment, because I would be utterly shocked if
it weren't truthy. It is, so the world makes sense so far.

    > truthiness(0);
    'falsey'

Okay, like many other languages, `0` is falsey in Javascript.

    > truthiness(null);
    'falsey'
    > truthiness(undefined);
    'falsey'

Likewise for `null` and `undefined`, which makes sense. I wonder what the
difference `null` and `undefined` is? I'll have to make up another specimen to
figure it out later.

    > truthiness([]);
    'truthy'

In some languages (Python), empty arrays are falsey. Not so in Javascript.

    > truthiness('');
    'falsey'

Would you believe this totally surprised me? I didn't know that Javascript
considered empty strings falsey. My world spinning, I checked to see if the
quote type matters (that would be really weird, but I just discovered
something I didn't know about a language I know really well, so I was prepared
for weirdness).

    > truthiness("");
    'falsey'

Okay. Whew. Empty strings are falsey no matter how they're quoted. And thank
god, I would have to rethink all my quotation marks if that weren't so.

    > truthiness({});
    'truthy'

Like the array example, I thought it might be possible that empty objects were
falsey. They are not.

Okay. Now that I know what I'm working with, I'm going to return my attention
to figuring out what the `||` operator does when both its operands are falsey.
My hypothesis is: `x || y` has the value of `x` if `x` is truthy, otherwise
it's `y` if `y` if truthy, and if neither is truthy, it's `false`. Thus, I
expect both these specimens to evaluate to `false`.

    > false || 0
    0
    > undefined || ''
    ''

Ah, I was wrong! They both evaluated to the second operand. So here's my new
hypothesis: `x || y` has the value of `x` if `x` is truthy, otherwise it's
`y`. That's simpler, so I can see why it's defined that way. I'm going to
hypothesize that `x && y` follows a similar pattern: it has the value of `x`
if `x` is falsey, otherwise it's `y`. Let's check that out:

    > null && 'hello'
    null
    > true && 'hello'
    'hello'
    > 'hello' && 'world'
    'world'

My theory about `&&` seems to hold.

*__What is the value of the expression `'dione' || 'rhea'`?__*

Based on my experiments, I'm going to guess it's `'dione'`.

    > 'dione' || 'rhea'
    'dione'

And the experiment validates my guess.
 
*__After the second assignment of `y`, `var y = y || {};`, has `y` changed? Why not?__*

Let's see what we're looking at here:

    var y = 'dione' || 'rhea';
    var y = y || {};

In the first assignment, we set `y` to the value of the expression `'dione' || 'rhea'`,
which we know is `'dione'`.

In the second assignment, we set `y` to the value of the expression `y || {}`.
Since `y` is already `'dione'`, which is truthy, I expect that `y || {}`
evaluates to `y`. So since `y` is truthy, we expect `var y = y || {}` to
essentially be `var y = y`, so I wouldn't expect the value of `y` to change.

    > var y = 'dione' || 'rhea';
    undefined

    > y
    'dione'

    > var y = y || {};
    undefined

    > y
    'dione'

And indeed, it hasn't.

*__What does `var z = z || {};` do?__*

This one is more interesting. `z` isn't previously defined. I think that means
its value is undefined, but let's check:

    > z
    ReferenceError: z is not defined
        at repl:1:2
        at REPLServer.self.eval (repl.js:110:21)
        at Interface.<anonymous> (repl.js:239:12)
        at Interface.emit (events.js:95:17)
        at Interface._onLine (readline.js:202:10)
        at Interface._line (readline.js:531:8)
        at Interface._ttyWrite (readline.js:760:14)
        at ReadStream.onkeypress (readline.js:99:10)
        at ReadStream.emit (events.js:98:17)
        at emitKey (readline.js:1095:12)

Huh. That's only slightly helpful. It looks like referencing variables that
aren't defined throws an error. Which, when I type that sentence out loud, I
probably could have predicted. But! I think that Javascript might convert that
exception into the value `undefined` when we use it in an expression, so I'm
going to stick by my prediction that `z` will be `undefined`, and so `z || {}`
will be `{}`, and so `var z = z || {}` means `var z = {}`. Let's check!

    > var z = z || {};
    undefined
    
    > z
    {}

Cool. Looks like my guess was right.

**You will see this a lot.** Specifically, you'll often see Javascript files
start with something like:

    var MyModule = MyModule || {};
    
    MyModule.someFunction = /* ... */

Why? Well, say I'm writing a library like jQuery. There are several files
involved, each of which add things to the jQuery object. If each of them said,

    var jQuery = { /* ... */ };

Then they would step on each other. They would all redefine `jQuery` to
contain only their functions. If we made it so that only one of them
(jquery.js, say) defined `jQuery`, then it would matter what order our script
tags were in. That's undesirable. What's more, if our scripts are loaded
asynchronously (with `<script async>`), then our program might fail sometimes
(when jquery-ui.js happened to load before jquery.js) but not all the time
(when they loaded in the expected order). That would be really irritating to
track down. We could say,

    if (typeof MyModule === 'undefined') {
      var MyModule = {};
    }

But that's a lot more verbose.


*__What does `f2` return? (That is, what is the value of the expression `f2()`?)__*

`f2 doesn't have a return statement, so I think the value of the expression
`f2() will be undefined.

    > var f2 = function() {
      console.log('enceladus');
    };
    undefined

    > f2()
    enceladus
    undefined

Note what's happening here: calling `f2()` prints 'enceladus' to the console
(the first line), but returns undefined (the second, where console is printing
the value of the statement I just evaluated). To check this, we can capture
the return value in a variable:

    > var x = f2();
    enceladus
    undefined
    
    > x
    undefined

Unlike Ruby, in Javascript, functions must explicitly `return` something to
return a value.

*__What does `var a = a || f2() || f1();` print, and what is the value of a afterwards? What would happen if we swapped the calls to `f2` and `f1`? Explain what's going on here. How does Javascript evaluate boolean expressions like `x && y` or `false || f() || true`?__*

Let's tease this apart. The || operator takes two operands: a left side and a
right side. Therefore, Javascript must interpret `a || f2() || f1()` as either
`(a || f2()) || f1()`. (Or `a || (f2() || f1())`, but in this case they're
equivalent. Why?)

So let's see what happens as we evaluate the expression. I'm going to bold the
terms as we evaluate them:

<pre>
<code>
      (<b>a</b> || f2()) || f1()
    = (undefined || <b>f2() /* prints 'enceladus' */</b>) || f1()
    = <b>(undefined || undefined)</b> || f1()
    = undefined || <b>f1()</b>
    = <b>undefined || 'mimas'</b>
    = 'mimas'
</code>
</pre>

So I expect this to print `'enceladus'` and evaluate to `'mimas'`.

    > var a = a || f2() || f1();
    enceladus
    undefined

    > a
    'mimas'

Good mental model! Now let's flip `f1()` and `f2()`:

<pre>
<code>
      (<b>a</b> || f1()) || f2()
    = (undefined || <b>f1()</b>) || f2()
    = <b>(undefined || 'mimas')</b> || f2()
    = <b>'mimas' || f2()</b> /* wait... was f2() ever called? */
    = 'mimas'
</code>
</pre>

Ah, so that's the question that the specimen is asking us to answer: if we say
`x || y` to Javascript, and `x` is truthy, is `y` even evaluated?

    > var a = a || f1() || f2();
    undefined

    > a
    'mimas'

`'enceladus'` wasn't printed, so it looks like no. The term for this is 'short
circuiting'—if the first operand to `||` is truthy, Javascript doesn't even
evaluate the second. I bet it can be anything:

    > f1() || kdjsfiuahlweflahef()
    'mimas'

This behavior shouldn't surprise you too much coming from Ruby land. Ruby also
behaves in this way. This explains why you can things like,

    @something < 5 || die("something isn't right.");

die won't be called if the left hand side of the expression is true.
 
### Finally, imagine I asked you to write an `or` function, which functions exactly like the `||` operator.

    // or(x,y, z) is identical to typing x || y || z
    function or() {
      // TODO
    }

Explain why this is impossible (and, if you like, how you could achieve something similar).

Short circuiting is the answer here. Sure, I could write this function:

function or() {
  for (var i = 0; i != arguments.length; ++i) {
    if (arguments[i]) return arguments[i];
  }
  return arguments[arguments.length - 1];
}

But when I called it, Javascript would first evaluate all its arguments so that it could have their values. The || operator doesn't do that.

    > or(undefined, false, 'hi', false)
    'hi'
    
    > or(f1(), f2())
    enceladus
    'mimas'
    
    > f1() || f2()
    'mimas'

So even though my function returns the same value, its side effects—what functions get called to evaluate it—are different.

If I wrote my function to take functions as arguments rather than values, I could get closer

    function orFuncs() {
      for (var i = 0; i != arguments.length; ++i) {
        var value = arguments[i]();
        if (value) return value;
      }
      return value;
    }

    > orFuncs(f1, f2)
    'mimas'

Note the lack of `()` after `f1` and `f2`—I'm not calling them and passing in their return values, I'm actually passing in (pointers to) the functions themselves. They get called on the line `var value = arguments[i]();`.

If that seems like black magic right now, don't worry. Our next specimen will be about passing around functions as data.

If this seems like a looooooot of energy to understand one operator, it's in part because I want to show you a general technique for understanding languages. Human languages are lists of sentences. Each sentence consists of phrases, and each phrase has something like a value. Robot languages are exactly the same in this regard, almost as if the same people who made human languages made robot languages. To understand what code is doing, you can pull it apart into its constituent bits and ask what they evaluate to.

[Here's a nifty tool](http://esprima.org/demo/parse.html) that does the first part for Javascript. It parses Javascript and turns it into JSON giving you a robot's eye view of your code—this is what the Javascript interpreter sees when it looks at your program.
