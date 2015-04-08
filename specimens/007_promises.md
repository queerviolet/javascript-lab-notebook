---
title: Specimen 007 — Promises
layout: specimen
category: specimen
permalink: /007_promises/
prev: /006_prototypes/
next: /
---

# Specimen 007: Promises #

    function win() {
      return new Promise(function(resolve, reject) {
        resolve('congratulations.');
      });
    }

    function lose() {
      return new Promise(function(resolve, reject) {
        reject('sorry.');
      });
    }

    function play(game) {
      return game.then(function(result) {
        console.log('you win:', result);
      }).catch(function(failure) {
        console.log('you lose:', failure);
      });
    }

    function many() {
      var games = Array.prototype.slice.call(arguments);
      Promise.all(games)
        .then(function() {
          console.log('all games won');
        })
        .catch(function() {
          console.log('at least one game lost')
        });
    }

1. What does `play(win())` print? What about `play(lose())`?
2. What about `many(win(), win())`? `many(win(), lose())`?
3. Write the body of this function:

    <pre><code>
    // Resolves in ms milliseconds.
    function sleep(ms) {
      // TODO
    }
    
    // Example (alerts after two seconds):
    sleep(2 * 1000).then(function() {
      alert('yawn!');
    });
    </code></pre>

4. Write the body of this function:

    <pre><code>
    // timeout(ms, [promise1, promise2...])
    // Resolves if all promises have resolved within ms.
    // Otherwise, rejects.
    function timeout(ms) {
      // TODO
    }
    
    // Example (resolves)
    timeout(1000, sleep(900), sleep(50), sleep(100)).then(function() {
      alert('quick enough.');
    });
    
    // Example (rejects)
    timeout(1000, sleep(10), sleep(2000), sleep(200)).catch(function() {
      alert('not nearly');
    });
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

