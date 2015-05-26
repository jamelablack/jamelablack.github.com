---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 1: From Test Later to Test Driven"
tags: tealeaf
---
##There are several ways to integrate testing into your workflow:

####1. Code First, Test Later
Benefit: Guard against regression

####2. Code a Little, Test a Little
Benefit: Feedback loop is shorter

####3. Test First Development
Where you write the test first, run it and make sure it fails then after which you write the code to
make it pass. It challenges to think about your expected result upfront and you're able to lock in your behavior
while working towards that

####4. Test Driven Development (TDD)
Test, not only to test with an expected result in mind but you write test to interactively build up (drive out) your
implementation code.
Benefit: people normally gravitate towards TDD, when they have an end goal that is complex in nature so they start
with the simple and build up on complexity gradually.

####5. Test Driven Design (TDDe)
Similar to Test Driven Development, except TDD is more logic and locally focused. while TDDe is more
expanding to the system level, more focused on the architecture (the interaction or collaboration b/w components in
your system - describing the interactions b/w various objects)
