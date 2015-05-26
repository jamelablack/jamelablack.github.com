---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 2: Shoulda Matchers"
tags: tealeaf
---

With rspec, there comes built-in matchers, which can be found: [shoulda matchers](http://www.relishapp.com/rspec/rspec-expectations/v/2-9/docs/built-in-matchers)


###1. Object Identity:
You should use "Object Equivalence" instead because you will be testing if the
two objects are the same when in reality they may equal the same but be different objects

* actual.should == expected
* actual.should eq(expected)
* actual.should eql(expected)
* actual.should equal(expected)

###2. Comparison
actual should.be > expected

###3. Types of Classes
test whether the actual is an instance of a class

###4. Truthiness and Existentialism
tests whether the actual is truthy(not nil or false)

###5. Expecting errors

###6. Expecting Throws
Expecting methods to throw a symbol
* Predicate Matchers - passes if method returns a truthy result

###7. Collection Membership
testing whether an array is a subset of the expected

###8. Ranges (only for Ruby 1.9)

Also matchers worth mentioning:

- Have(n)items matcher
- Operator matchers
- Respond_to matchers - does this object respond to a certain method
- Satisfy matcher - very flexible, you can pass it a block to see if it will evaluate to true
- Specify a_kind_of matcher
- Yield matchers - allow you to work with blocks
- End_with, start_with
- Cover Matchers
