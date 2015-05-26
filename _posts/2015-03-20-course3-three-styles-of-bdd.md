---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 5: Three Styles of BDD"
tags: tealeaf
---
##Testing Styles in Rails: Three Styles of Behavior Driven Development

Traditionally we have followed the following flow:
###Meet in the Middle Technique
1. Turn mock ups into view templates:
copy the mockups and replace the placeholder data
2. Controller Tests (inner layer)- the controller tests will drive the model layer
tests, routes, db migrations, etc.
3. Model Level Tests(inner layer) - if we need active record based and non-active record based
model - logic, we will need to drop down to model layer to test their
4. Feature Specs/Integration - (Outer most layer) Tests will allow us to test everything including the view templates

- Here you will end up with less feature/integration specs than outside in because they are typically written after multiple specs.

###Inside out Approach
1. Start from the models and migrations that are required for that feature and then
right specs to test that model
2. Controller Level
3. View/Intergation Specs

- easier to start .. allows you to work with something small
- But you sometimes don't know where you're going and you may end up
adding alot of code that is not needed.
- This is used primarily in productized shops where its easier to add a model to a more mature product.


###Outside in Approach
Start at the Integration Level - This integration level does not mean in the
same way that we reference the Integration Tests in the "Meet in the Middle" technique where where we test multiple features (horizontal) but rather vertical -integration means where we test for models, controllers and views for one feature
- written in a very business level decision level - you have to think 'what is the business value we are looking to provide?'

- route, controller, models
- also called BDD or ABDD (A is for Acceptance)
- often used in consultancys, clients provide the feature
- often follows more a natural flow
- requires you to know rails really well and how things work together
- people often use cucumber or rspec feature tests for this.
