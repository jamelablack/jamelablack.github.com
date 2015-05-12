---
layout: post
title: "Intro to Ruby & Web Dev: Lesson 2"
tags: tealeaf
---

#Items covered:
1. Classes, Instances, & Objects
2. Modules
3. Inheritance
4. Method Lookup Chain
5. Procedural vs OOP

##Classes & Objects
* Objects are instantized through classes and contant states and behaviors
* Classes are the cookie cutters
* Instance methods encapsulate behaviors and are defined in classes.
* Instance variables = state
* Instance methods = behaviors
Interesting syntax when using equals, setter:

{% highlight ruby %}
def set_name=(new_name)
  @name = name
end

jamela = User.new

jamela.set_name = "Jam"
{% endhighlight %}
or

{% highlight ruby %}
def set_name(new_name)
  @name = name
end

jamela.set_name("Jam")
{% endhighlight %}

Setters:

You have to use .self when using the setter method within an instance method of the same class.
Instance methods are like raw data and you can access them but in general, you always want to use the getters and setters when available for encapsulation and santization.

###Class Variables:

Track states that are common across all instances within a class.
{% highlight ruby %}
#To keep track of number of objects
@@class = 0

def initialize(n, h, w)
  @name = n
  @height = h
  @weight = w
  @@count += 1
end

@@class_variable +=1
{% endhighlight %}

###Class Methods:

Performs behaviors at the class level
{% highlight ruby %}

def self.total_count
"The total number of dogs is #{@@count}"
end

puts Dog.total_count
{% endhighlight %}

##Inheritance
SubClass < SuperClass

object.ancestors shows you the method look-up chain

##Namespace
class User < ActiveRecord::Base
refers to the inheritance of the Base class inside the ActiveRecord module
Just like with Cms::User - refers to the User class in the CMS module.

##Modules
Allows you to mixin in behavior. The difference between modules and classes are that modules cannot be instantiated.
{% highlight ruby %}
module Swimmable
  def swim
  puts "I'm swimming!"
  end
end

{% endhighlight %}

In method, you can mixin in the module by typing "include Swimmable".
When creating modules you simply add "-able" to the end of your method name and then type "include swimmable" in the method you want to add that behavior.

-In method lookups, a module that is mixin will superecede (be listed first) super classes. This means that if a module and a superclass has the same method, the module method will have precedence. When mixin two modules, the method lookup chain will be effected based on reverse inclusion, meanging the last module included will be looked at first.

The point of modules is extract code that is non-specific to a class... and make it reusable.

Some modules can require methods or behaviors:
{% highlight ruby %}

module Fetchable
  def fetch
  #{name} is fetching!
  end
end
{% endhighlight %}
The above module requires the name getter method look up chain

##OOP vs Procedural
Procedural thinks about "what happens next..." and small changes can have big impacts throughout your code.

OOP thinks in terms of objects and how they behave Generally consider classes to be nouns and verbs to be methods. Start by first writing out requirements in pseudo-code.


