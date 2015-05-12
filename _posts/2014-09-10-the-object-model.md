---
layout: post
title: "Intro to Ruby & Web Dev: The Object Model"
tags: tealeaf
---

##What is OOP?
A way for progammers to deal with large, complex systems.
One small change could be catastrophic in terms of the impact it could have on relevant parts within the system.
Prorgrammers needed to create containers for code that could be changed without impacting the entire program. To section off code that performs certain procedures so that it became an interation of many small parts as opposed to one massive blob of code dependenies.

##Encapsulation
is hiding peices of functionality and making it unavailable to the rest of the code base. It is form of data protection so that the data cannot change or be manipulated without intention. This allows your code to achieve new levels of complexity creating objects and exposin interfaces (i.e. methods) that allow these methods to interact.

##Polymorphism
ability for data to be represented as many different types.
Allows pre-written code to be used for different purposes.

##Inheritance
class inherits the behavior of another class -- referred to as the superclass.
This gives Ruby Programmers the ability to define large usabilty and smaller Subclass, with more defined, detailed behavior.

##Modules
Modules are similar to classes in the they contain shared behavior, however you cannot create an object with a module. A module, must be mixed in with a class using the reserved word: include. This is called a "mixin" and after used in a module, the behanviors declared in the module are available to the class and its objects.

##What are objects?
In Ruby, everything is an object.
Objects are created from classes.

###Class
Ruby defines attributes and behaviors of its objects in classes

###Instantation
creating an object/instance of a class

###Module
A collection of behaviors that useable by other classes, called mixins using the word "include"

Example:
Create a class, create an object, use a mixin.

{% highlight ruby %}
module Speak
  def speak(sound)
    puts #{sound}
  end
end

class HumanBeing
  include Speak
end

jamela = Humanbeing.new

jamela.speak('Hello!')
{% endhighlight %}
