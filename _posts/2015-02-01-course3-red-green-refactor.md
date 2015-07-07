---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 1: Red, Green, Refactor"
tags: tealeaf
---
Generalization or Trianglization:
Writing general code

##Red-Green Refactor
* Writing Tests to Fail and then refactor your code to pass.

* Each time - only write enough code to get an different error message.

TDD is a discipline for writing code, writing developer (unit) tests, and doing design work in an integrated approach. A useful mnemonic for remembering the basic TDD cycle is "red > green > refactor." Here is a short summary of each of the steps.

###RED
Start by thinking of a behavior you want the software to have which could be implemented by a few lines of code (fewer than five is a good target). Then write a test (also only a few lines of code) that will fail unless that behavior is present.

###GREEN
Write just enough code to get the test to pass. Focus on: Getting something to work. Simplest possible implementation, rather than the most elegant solution (even hard-coded values are okay).

###REFACTOR
Now that the code does what you want, use refactoring to make the design right. Making the code readable and eliminating duplication.

The cycle then repeats with each new behavior you want to have. The entire cycle should take between 5-10 minutes.

A couple of last points to emphasize:

Tests are checked in with the rest of the code (they are essentially living documentation of the code)
Tests are run with every build


Good Article by Kent Beck: [Test Driven Development](http://gmarik.info/notes/programming/test-driven-development-kent-beck)
