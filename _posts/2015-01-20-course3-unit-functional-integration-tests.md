---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 1: Unit vs. Functional vs. Integration Tests"
tags: tealeaf
---
###There are 3 types of tests:
1. Unit
2. Functional
3. Integration

###Ranking according to:
####Coverage:
Unit > Functional > Integration
####Speed:
Unit > Functional > Integration
####Realistic:
Integration > Functional > Unit

###1) Unit Tests
- testing a component in isolation, focusing on 1 thing and making sure all cases are covered
- isolated components tested: routes, models, views, helpers
- allow you to thoroughly test all components.. as it is the closest to the code.
- is the fastest and gives you the best coverage



###2) Functional Tests
- testing components in collaboration, making sure not only they work but that they play nicely with each other
- components tested: controllers (where everything interacts), typically once request/response cycle


###3) Integration Tests
Builds upon functional tests in the sense that not only must play nicely together but they must achieve a desired business objective.

* Components tested: Goal is to emulate the user, typically occurs in browser and follows the entire business process and
that the business objective is achieved

There are multiple styles of testing.. it has become very much like an "art" as devs often times develop a taste of styles for what to test, when, etc. In this course, we will primarily write unit and functional tests leaving integration tests for important business workflows.
