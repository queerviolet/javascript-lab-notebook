---
title: Specimen xx0 — Function Composition
layout: specimen
permalink: /xx0_function_composition/
prev: /002_callbacks_and_asynchrony/
next: /001_short_circuit_evaluation/
---

# Specimen xxx0: Function Composition #

Note: This specimen is culled from [specimen 002](002_callbacks_and_asynchrony), and
is not yet complete.

    function callMeDefinitely(func) {
        console.log('entering callMeDefinitely');
        func();
        console.log('returning from callMeDefinitely');
    }
    
    function linear(min, max) {
        var range = max - min;
        return function(t) {
            return min + t * range;
        };
    }
    
    function clamp(min, max, t) {
        if (typeof t == 'undefined') {
            return function(x) {
                if (x < min) return min;
                if (x > max) return max;
                return x;
            };
        } else {
            return clamp(min, max)(t);
        }
    }
    
    function join(functions) {
        var clampToIndex = clamp(0, functions.length - 1);
        return function(t) {
            var which = clampToIndex(Math.floor(t * functions.length));
            var piece = functions[which];
            return piece((t * functions.length) % 1.0);
        };
    }

1. What does `names(function(x) { console.log('athena', x); })` print, when, and in what order?
2. What does `callMeDefinitely(function() { console.log('hi'); })` print, when
   and in what order?
3. In Javascript, when I pass a function as an argument, when will that function
   be called?
4. What type of objects do the functions `linear`, `clamp`, and `join` return?
5. What does the expression `clamp(0, 1)(2)` evaluate to? What about `clamp(0, 1, 2)`?
6. The `linear` function takes a `_________` and a `_________` and
   returns a function that `_______________`.
7. The `clamp` function takes `__________` and returns `____________`.
8. The `join` function `_________________`.
9. In Javascript, how are functions different from any other type of object?
10. When might you use a pattern like the one exemplified by `linear`, `clamp`, and `join`?


# Ashi's Notes #


## 1. What does `names(function(x) { console.log('athena', x); })` print, when, and in what order?

Here's what happens for me when I do that:

    > names(function(x) { console.log('athena', x); })
    saraswati gaia
    undefined
    > athena undefined
    kali gaia
    inana tiamat

Okay, now let's see if we can understand why. Here's `names()`:

    function names(func) {
        var x = 'gaia';
        setTimeout(function() {
          x = 'tiamat';
          console.log('inana', x);
        }, 3000);
        setImmediate(func);
        setTimeout(function() {
          console.log('kali', x);
          x = 'lakshmi';
        }, 200);
        console.log('saraswati', x);
    }

Let's make a little timeline. I'm going to assume that each Javascript statement
takes about a millisecond to execute. That's about a thousand times slower than
real time, but because the timeouts are spaced pretty far apart, so it shouldn't
matter for figuring out what order things happen in.

### t = 0
    names(function(x) { console.log('athena', x); })

We call `names` and pass it a single argument.

The argument we pass is function which takes one argument, `x`, calls
`console.log('athena', x);`, and returns `undefined`. Note that we haven't
ever called this function yet—we've just defined it. We could have just as well
said,

    names(new Function('x', 'console.log("athena", x);'));

This is something you can actually do (though I don't recommend it, because
writing code in a string is pretty ugly). In Javascript, functions are just a
kind of object (a special kind, which you can call with these: `()`).

### t = 1
    var x = 'gaia';

We assign the variable `x` to the string `'gaia'`. `x` is local to `names`.
That is, it's defined within the stack frame of `names`. Here's what the stack
looks like after this assignment:


* __`names (line 2)`__: `func: function(x) { console.log('athena', x); }, x: 'gaia'`
* __`<node console>`__: `names: [Function: names]`

### t = 2
        setTimeout(function() {
          x = 'tiamat';
          console.log('inana', x);
        }, 3000);

Now we call `setTimeout`, passing it two arguments: a function, and the number
3000. [The `setTimeout` documentation](https://developer.mozilla.org/en-US/docs/Web/API/WindowTimers.setTimeout)
says that the function will get called at around t = 3002 ms.

So where does the function that we passed in go? Is it stored in an object
somewhere? The documentation doesn't say, so it's up to the Javascript implementation.
I presume that somewhere, there is an object that stores timers. Let's say it's
just an array of objects that each have a time and a function. I think it must surely
be a bit more more involved than that, but it's a good enough model for now. Let's
pretend that this object is stored in global.timers. This is definitely not true, but
that's okay. Before we ran this code, that object looked like this:

     global.timers = []

After calling setTimeout, that it looks like this:

     global.timers = [{time: 3002,
                       func: function() {
                           x = 'tiamat';
                           console.log('inana', x);
                       }},]

When we call `setTimeout`, it gets pushed onto the stack with its arguments:

* __`setTimeout`__: `func: function() { x = 'tiamat'; console.log('inana', x); }, delay: 3000`
* __`names (line 3)`__: `func: function(x) { console.log('athena', x); }, x: 'gaia'`
* __`<node console>`__: `names: [Function: names]`

Then it does its thing (adding `func` to `global.timers`), and returns, so the
stack looks like this:


* __`names (line 6)`__: `func: function(x) { console.log('athena', x); }, x: 'gaia'`
* __`<node console>`__: `names: [Function: names]`

Where we were before, though we've moved a few lines down to the next statement.

We haven't printed anything yet, and the only functions that have been called are
`names` and `setTimeout`.

### t = 3

        setImmediate(func);

This is interesting. What does `setImmediate` do? It's a new thing, I'm not very
familiar with it. I'm going to assume it doesn't just call `func`—there's already
a way to write that, `func()`, and it's shorter. So I'm going to presume it does
something very like `setTimeout(func, 0)`. The [documentation](https://developer.mozilla.org/en-US/docs/Web/API/Window.setImmediate) makes some noise about how it's not really a standard and probably
not going to become one, and says that `setTimeout(func, 0)` isn't a good substitute
for some reason that has to do with the resolution of the timers that `setTimeout`
is implemented with. Since we're doing a toy demo, not writing some async framework,
I don't think we care about that.

So after running this, our timers object looks like this:

     global.timers = [{time: 3,
                       func: function(x) {
                            console.log('athena', x);
                       }},
                       {time: 3002,
                       func: function() {
                           x = 'tiamat';
                           console.log('inana', x);
                       }},]

But wait: that timer is set to go off... now. But it can't, because we're in the
middle of running our function, and Javascript has one thread and can't interrupt
functions while they're running.

Whatever code that comes along and actually runs these timers will have
to run all timers that should have gone off since the last time it was able to
run. I guess that's why [the `setTimeout` documentation](https://developer.mozilla.org/en-US/docs/Web/API/WindowTimers.setTimeout) documentation says that timers may or may not run
exactly when you ask them to.

### t = 4

        setTimeout(function() {
            console.log('kali', x);
            x = 'lakshmi';
        }, 200);

Now our timers table looks like this:

     global.timers = [{time: 3,
                       func: function(x) {
                            console.log('athena', x);
                       }},
                       {time: 204,
                        func: function() {
                            console.log('kali', x);
                            x = 'lakshmi';
                       }},
                       {time: 3002,
                       func: function() {
                           x = 'tiamat';
                           console.log('inana', x);
                       }},]

You'll notice that I'm inserting the timers in sorted order. I have no idea if
that's what Javascript engines actually do, but it's nice for our purpose, which
is figuring out when these functions run.

### t = 5

        console.log('saraswati', x);

OMG we finally get to print something! The first thing we print is `'saraswati'`,
followed by the value of `x`. What's the value of `x`? Well, let's look at our
stack frame:

* __`names (line 11)`__: `func: function(x) { console.log('athena', x); }, x: 'gaia'`
* __`<node console>`__: `names: [Function: names]`

Okay, it's `'gaia'`. None of the other functions have run yet, so the value of
`x` hasn't changed. So the first thing we expect to see is `saraswati gaia` (and
indeed, that's the first thing that's printed).

### t = 6

Now we return from names. The stack looks like this

* __`<node console>`__: `_: undefined`

`_` is the name that the console gives to the value of the last expression you
typed, which it then dutifully prints. This is where the `undefined` comes from
in the print out above.

And then we return from `<node console>`, which is the dummy name I gave to the
function that's evaluating what I typed at the console.

I was calling it one function for simplicity, but actually, there are a whole lot
of functions that call each other when you type something at the console and hit
enter. To see what they are, you can throw an error from the Node console. The
error will bubble up through stack frames. When it hits the top frame, Node will
dutifully print its stack trace:

    > throw new Error();
    Error
        at repl:1:7
        at REPLServer.self.eval (repl.js:110:21)
        at repl.js:249:20
        at REPLServer.self.eval (repl.js:122:7)
        at Interface.<anonymous> (repl.js:239:12)
        at Interface.emit (events.js:95:17)
        at Interface._onLine (readline.js:202:10)
        at Interface._line (readline.js:531:8)
        at Interface._ttyWrite (readline.js:760:14)
        at ReadStream.onkeypress (readline.js:99:10)

So that's a lot of stuff. Read it from the top. The first line says `at repl:1:7`.
That's the line of code that I typed. `repl`, which stands for Read Eval Print Loop,
is the dummy filename that Node gives to stuff you type at the console.

Then it looks like there are some functions that handle reading and evaluating
Javascript as it's typed at the console. `REPLServer.self.eval` probably
`eval()s` things. `Interface.<anonymous>` looks like an anonymous function (which
is just a function without a name, like this: `function(x) { }`) that processes
the lines which are emitted by `Interface.emit`, which is in turn called by
`Interface._onLine`. `Interface._onLine` has a leading underscore, so I suspect it's
an internal function that's called when a `'\n'` is encountered in the input stream—that is,
when I've hit ENTER. `Interface._ttyWrite` looks like it handles talking to a
[TeleTypeWriter](http://en.wikipedia.org/wiki/Teleprinter). The teletypewriter, in this
case, is my Terminal window, which is still, after all these years,
[pretending to be a typewriter](http://en.wikipedia.org/wiki/Terminal_emulator).

`ReadStream.onkeypress` is much more familiar: it looks like an event handler that
gets called when I press a key.

And then there's nothing. No more Javascript stack frames.

### t = 7

At this point, there are no more Javascript frames on the stack. In most programming
languages, when there are no more frames on the stack, the program exits.

That, clearly, is not how Javascript works.

But if there are really no more Javascript stack frames, how will our functions ever
be called?

Underneath the Javascript stack sits the Javascript engine. In the engine
is the event loop. You can think of this as an infinite loop that just sits there
spinning. Each time around the loop, it asks, "have any new events come in? are any
timers due?" and if they have or there are, it goes and calls the appropriate
functions one at a time. It knows what the appropriate functions are by looking
them up in a table not unlike the one we made. And if there's nothing to do, it
just sits and spins until there is.

     global.timers = [{time: 3,
                       func: function(x) {
                            console.log('athena', x);
                       }},
                       {time: 204,
                        func: function() {
                            console.log('kali', x);
                            x = 'lakshmi';
                       }},
                       {time: 3002,
                       func: function() {
                           x = 'tiamat';
                           console.log('inana', x);
                       }},]

In this case, it doesn't have to wait long, because there's a timer that was
supposed to go off at t = 3, and it is now t = 7. "Oops," the event loop does not say,
because it is a program and does not say anything, "I guess I'll do that now". So it
calls `function(x) { console.log('athena', x); }()`, and prints `athena undefined`.

Why doesn't it print `athena gaia`? Because `x`, you'll notice, is passed as an argument
to this function. It doesn't refer to the `x` in `names`. But when the event loop calls
timer functions, it doesn't call them with any arguments, so `x` will just be `undefined`.

I feel kindof bad for putting that bit in there. It's important, but more than a little
confusing.
     
### t = 7...203

Nothing happens for a long time. I wonder if the event loop gets lonely?

### t = 204

At this point, the event loop wakes up again and calls `function() { console.log('kali', x); x = 'lakshmi'; }`.
In this case, `x` *does* refer to the `x` in names, because this function was defined within
`names`, and because `x` isn't declared with `var` inside this function. If the semantics of
what names refer to what variables when is confusing, don't worry, that's the next notebook
page.

At this point, our program will print `kali gaia`, and then set the value of `x` to `'lakshmi'`.

### t = 3002

The event loop calls our last timer. We never print `'lakshmi'` (sorry, hon), because `x` is
immediately redefined to `'tiamat'`. The last line we print, which pops out three seconds
and change after I hit enter, is `inana tiamat`.

## The short version

That was a considerably longer timeline than I expected to write. I guess I have a lot
to say about asynchronous programming in Javascript. The reason I have a lot to say
is that it is, frankly, confusing. I don't really like that Javascript (and many other
languages) have adopted asynchronous APIs, because it can make your code either
somewhat harder to read, or a lot harder to read. We tend to read computer programs
like this:

  1. Turn on the oven to 350 degrees.
  2. Mix the flour and water to make dough.
  3. Wait until the oven is at 350 degrees.
  4. Put the dough in the oven.
  5. Wait 20 minutes.
  6. Remove the bread from the oven.
  7. Eat the bread.

But you literally cannot express this in Javascript, because you cannot say "wait".
Javascript does not wait, ever. What you say instead is,

  1. Turn on the oven to 350 degrees.
  2. Mix the flour and water to make dough.
  3. When the oven is at 350 degrees:
     1. Put the dough in the oven.
     2. After 20 minutes:
       1. Remove the bread from the oven.
       2. Eat the bread.

What I often see people do is this:

  1. Turn on the oven to 350 degrees.
  2. Mix the flour and water to make dough.
  3. When the oven is at 350 degrees:
     1. Put the dough in the oven.
     2. After 20 minutes:
       1. Remove the bread from the oven.
  4. Eat the bread.

Which I get. If you wrote that on a piece of paper, a human being would get what
you meant. A computer, of course, gets to step 4 and is like, "your bread is seriously
undefined." And if you just look at the code, you're like... but I totally defined it!
I just said "remove the bread from the oven"!

Javascript has some patterns to make expressing this much clearer, and we'll look
at them in a future specimen.

That was long and detailed, so the rest of my answers are pretty short and to the
point. You can always use the same methodology we used here to figure them out in
more depth.

2. What does `callMeDefinitely(function() { console.log('hi'); })` print, when
   and in what order?

It prints `'hi'`, immediately, before it even returns.

3. In Javascript, when I pass a function as an argument, when will that function
   be called?

Whenever it gets called. If it even gets called. When you pass a function as
an argument, you're passing in a chunk of code. You have to look at the documentation
or the source code of what you're calling to figure out when your function gets
called, if it gets called, and with what arguments.

4. What type of objects do the functions `linear`, `clamp`, and `join` return?

`linear` and `join` return functions. `clamp` will return a number if you call
it with three arguments, or a function if you call it with two.

5. What does the expression `clamp(0, 1)(2)` evaluate to? What about `clamp(0, 1, 2)`?

They both evaluate to 1. `clamp(0, 1, 2)` actually calls `clamp(0, 1)(2)`. If
that syntax looks weird, just remember the previous specimen and take it
piece by piece. `clamp(0, 1)` returns a function. The `()` operator calls the
object that preceeds it if the object is a function, passing in the arguments
within the parentheses. If the object isn't a function, it barfs:

    > ("hello")()
    TypeError: string is not a function

So the slightly odd looking `clamp(0, 1)(2)` becomes:

     (function(x) {
        if (x < 0) return 0;
        if (x > 1) return 1;
        return x;
      })(2)

Which in turn becomes 1.

6. The `linear` function takes a `min` and a `max` and
returns a function that returns values ranging linearly from min to
max as `t` varies from `0.0` to `1.0`.

7. The `clamp` function takes a `min` and a `max` and returns a function that
takes a value `t` and returns `t` clamped to `(min...max)`, inclusive. That is,
if `t` is greater than `max`, the function returns `max`, and if it's less than
`min`, it returns `min`.

If clamp is called with a third argument, it passes the argument as `t` and
returns the result.

8. The `join` function takes an array of functions which each take an input
from `0.0` to `1.0`, and returns a function that takes an input from `0.0` to
`1.0` and returns a value in the range of one of the functions passed in. If four
functions `[a, b, c, d]` are passed in, then `join([a, b, c, d])(0.0` to `0.25)` will
return `a(0.0` to `1.0)`, `join([a, b, c, d])(0.25 ` to `0.5)` will return `b(0.0 ` to `1.0)`,
and so on.

9. In Javascript, how are functions different from any other type of object?

You can call them `likeThis()`. I find it quite annoying that there's no way to
write arbitrary callable objects in Javascript, but c'est la vie.

10. When might you use a pattern like the one exemplified by `linear`, `clamp`, and `join`?

I was using it to handle animations. You use `linear` to express the range you want
something to move. If you want to move something from 10px to 20px over 1 second, then
`linear(10, 20)` gets you a function that tells you where the object should be at any
given moment in time. `clamp` lets you set a maximum so it doesn't just keep moving,
and `join` lets you combine movements. There were a few other functions that let you
define how long something took (it does the inverse of linear, mapping timestamps
to `0.0` to `1.0`).

That's all for now. Try to do something mind expanding for New Year's, or at least
drink a lot of water. Actually, drink a lot of water no matter what you're doing.

In a few days, I'll post the next specimen. It will be simpler, I promise, and it
will be about closures and scoping.