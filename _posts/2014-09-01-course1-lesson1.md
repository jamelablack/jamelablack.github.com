---
layout: post
title: "Intro to Ruby & Web Dev: Lesson 1"
tags: tealeaf
---
#Items covered:
1. Hashes vs. Arrays
2. Debugging
3. Pass by Reference vs. Pass by Value
4. Enumerables

##Hashes vs. Arrays

##Debugging
How to debug?
 - Tips on how to work through bugs in your code or large problems where you don't know where to start.

1. Google - copy and paste error into Google. Someone has had your issue before.
2. Write out pseudocode - write what you are attempting to do in code in plain engish
3. Break problems into small, numerical steps - start with what looks familiar ( or you know how to do)
4. Ask questions in the forum (Tealeaf)
5. Use binding.pry
6. Paste Errors or problems in Gist - take issue to IRC chat
7. Rubberduck debugging.
When you come across a problem - explain how to came to that problem to your (fictitious) coworker - Ms. Rubberduck. Even better, explain the problem to someone who has no programming experience at all.
8. Step away from the problem.

##Pass by Value va. Pass by Reference
Some methods mutuate the caller, while others do not. To better understand this fact, let's look at:
Pass by Value vs Pass by Reference:
  Pass by Value - when a method is called on an object, the variable is just representation of the value and not the actual object (the reference), itself. When called, the method can never change the object itself. To effect change to the object, you must reassign the variable.

  In this case below, outter_str remains unchanged as a result of the method being called.
{% highlight ruby %}

def some_method(str)
  str.changeit
end

outter_str = "hi"
some_method(outter_str)

{% endhighlight %}
  _________________________________

  Pass by Reference - When a method is called, the object acts a pointer and a modification is actually made to an actual space in memory.

  In the case below, outter_str is just a reference (pointer) to str, therefore any mutations to str will be reflected in the value of str
{% highlight ruby %}
def some_method(str)
  str.changeit
end

outter_str = "hi"
some_method(outter_str)

{% endhighlight %}

  Ruby is Passing by the Reference of the Caller: Whether or not the method creates a pass by reference or pass by reference scenario depends on whether the method mutates the caller or not

##Enumerables

Tags:  <a href="{{ page.tags }}"> {{ page.tags}} </a>




