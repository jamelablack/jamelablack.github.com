---
layout: post
title: "Intro to Ruby & Web Dev: Method Definition vs. Method Invocation"
tags: tealeaf
---

##Method Definition
{%highlight ruby%}
def method(name)
  @name = name
end
{%endhighlight%}


##Method Invocation using self.
If you call a method that does not have a caller, ruby will append self. the caller
When calling the self. inside an instance method => the instance variable itself is returned.

=> self is an object from within the instance method, self changeses depending on what instance method is calling it.
Self being called outside of an instance method refers to the class.

* Use .inspect to see what self is.

1. Singleton (ghost) class shadows a particular object, only
  => will not show up in ancestors
  +> you can declare methods on objects and is only callable/accessible by that object.
2. All classes are objects also.
3. DSL - Doman Specific Language => example Rails is a DSL for Ruby, which gives you colligiual sytanx to work with.
4. Abtractions - all abstractions leak, not every code base is going fit 100% to a Specific way of writing.

All Classes are Objects also.
=> All classes are objects of the Class class
=> Everything that starts with a capital letter is a constant, including classes => that's why we can store methods in them.

