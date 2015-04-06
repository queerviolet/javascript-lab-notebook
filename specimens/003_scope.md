---
title: Specimen 003 — Scope
layout: specimen
category: specimen
permalink: /003_scope/
prev: /002_callbacks_and_asynchrony/
next: /004_call_apply_bind/
---

# Specimen 003: Scope #

    function one() {
      for (var i = 1; i <= 10; ++i) {
        console.log(i);
      }
    }

    function two() {
      for (var i = 1; i <= 10; ++i) {
        (function() {
          console.log(i);
        })();
      }
    }

    function three() {
      for (var i = 1; i <= 10; ++i) {
        setTimeout(function() {
          console.log(i);
        }, 0);
      }
    }


1. What do these functions print and why?

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