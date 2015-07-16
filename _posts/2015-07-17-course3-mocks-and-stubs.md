---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 8: - Mocks & Stubs"
tags: tealeaf
---

##Further Reading
Check out this [blog post](http://martinfowler.com/articles/mocksArentStubs.html) by Martin Fowler. Here is an excert I found valuable:

Meszaros uses the term Test Double as the generic term for any kind of pretend object used in place of a real object for testing purposes. The name comes from the notion of a Stunt Double in movies. (One of his aims was to avoid using any name that was already widely used.) Meszaros then defined four particular kinds of double:

Dummy objects are passed around but never actually used. Usually they are just used to fill parameter lists.
*Fake objects actually have working implementations, but usually take some shortcut which makes them not suitable for production (an in memory database is a good example).
*Stubs provide canned answers to calls made during the test, usually not responding at all to anything outside what's programmed in for the test. Stubs may also record information about calls, such as an email gateway stub that remembers the messages it 'sent', or maybe only how many messages it 'sent'.
*Mocks are what we are talking about here: objects pre-programmed with expectations which form a specification of the calls they are expected to receive.
Of these kinds of doubles, only mocks insist upon behavior verification. The other doubles can, and usually do, use state verification.


Also, check out this [talk ](http://confreaks.tv/videos/rubyconf2011-why-you-don-t-get-mock-objects) on Mocks
