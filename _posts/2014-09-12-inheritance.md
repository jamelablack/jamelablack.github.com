---
layout: post
title: "Intro to Ruby & Web Dev: Inheritance"
tags: tealeaf
---

#Inheritance
Subclasses inherit attributes and behaviors of superclasses.

When in cases of subclasses and superclass with the same class methods, the subclass will overide the superclass because ruby checks an object class before moving to the superclass.
n the GoodDog class, we're overriding the speak method in the Animal class because Ruby checks the object's class first for the method before it looks in the superclass. So, that means when we wrote the code sparky.speak, it first looked at sparky's class, which is GoodDog. It found the speak method there and used it.

##DRY
Inheritance can be a great way to remove duplication in your code base. There is an acronym that you'll see often in the Ruby community, "DRY". This stands for "Don't Repeat Yourself". It means that if you find yourself writing the same logic over and over again in your programs, there are ways to extract that logic to one place for reuse.

##Mixing in Modules

Another way to DRY up your code in Ruby is to use modules. We've already seen a little bit of how to use modules, but we'll give a few more examples here.

Extracting common methods to a superclass, like we did in the previous lesson, is a great way to model concepts that are naturally hierarchical.

##Inheritance vs Modules

Now that you know the two primary ways that Ruby implements inheritance, class inheritance and mixing in modules, you may wonder when to use one vs the other. Here are a couple of things to remember when evaluating those two choices.

You can only subclass from one class. But you can mix in as many modules as you'd like.
If it's an "is-a" relationship, choose class inheritance. If it's a "has-a relationship, choose modules. Example: a dog "is an" animal; a dog "has an ability to swim.
You cannot instantiate modules (i.e., no object can be created from a module) Modules are used only for namespacing and grouping common methods together.

##Method Lookup Path

Now that you have a grasp on both inheritance and mixins. Let's put them both together to see how that affects the method lookup path. Recall the method lookup path is the order in which classes are inspected when you call a method.

There are several interesting things about the above output. First, this tells us that the order in which we include modules is important. Ruby actually looks at the last module we included first. This means that in the rare ocurrance that the modules we mix in contain a method with the same name, the last module included will be consulted first. The second interesting thing is that the module included in the superclass made it on to the method lookup path. That means that all GoodDog objects will have access to not only Animal methods, but also methods defined in the Walkable module, as well as all other modules mixed in to any of its superclasses.

##Private, Public, and Protected Methods
Public Methods are available to all
Non-callable inside or outside a class but can be interpolated
Protected methods act as public methods inside the class, therefore can be called but act as private methods outside of the class.
{% highlight ruby %}
private

def human_years
    self.age / DOG_YEARS
end

sparky = GoodDog.new("Sparky", 4)
sparky.human_years
{%endhighlight%}

We get the error message:
{% highlight ruby %}
NoMethodError: private method `human_years' called for
  #<GoodDog:0x007f8f431441f8 @name="Sparky", @age=28>
{%endhighlight%}

We have made the human_years method private by placing it under the private reserved word. So what is it good for if we can't call it? private methods are only accessible from other methods in the class. For example, given the above code, the following would be allowed:

Assume the method definition below is above the "private" keyword
{% highlight ruby %}
def public_disclosure
  "#{self.name} in human years is #{human_years}"
end
{%endhighlight%}
Note that in this case, we can not use self.human_years, because the human_years method is private. Remember that self.human_years is equivalent to sparky.human_years, which is not allowed for private methods. Therefore, we have to just use human_years. In summary, private methods are not accessible outside of the class definition at all, and are only accessible from inside the class when called without self.
