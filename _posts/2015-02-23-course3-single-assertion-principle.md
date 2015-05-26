---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 2: Single Assertion Principle"
tags: tealeaf
---
##Single Assertion principle states:
A test suite should only have one assertion, so that if there is a failure
they fail separately. If you find yourself using "and" then you are probably
testing too much at once:
{%highlight ruby%}
  get :index
  assigns(:todos).should == (cook, sleep)
{%endhighlight%}

There is an exception to this rule - when the assertions are very related concerning the same object:
{%highlight ruby%}
  get :new
  assigns(:todo).should be_new_record
  assigns(:todo).should be instance_of(Todo)
{%endhighlight%}
