---
layout: post
title: "Refactoring - Simplifying Conditional Expressions"
tags: refactoring
---
###1. Decompose Condtional
You have a complicated conditional (if-then-else) statement.
Extract methods from the condition, “then” part, and “else” parts.
Mechanics
**1. Extract the condition into its own method.
**2. Extract the “then” part and the “else” part into their own methods.

###2. Recompose Conditional
You have conditional code that is unnecessarily verbose and does not use the most readable Ruby construct.
Replace the conditional code with the more idiomatic Ruby construct.
{%highlight ruby%}
before:
parameters = params ? params : []

after:
parameters = params || []
{%endhighlight%}

###3. Consolidate Conditional Expression
You have a sequence of conditional tests with the same result.
Combine them into a single conditional expression and extract it.

###4. Consolidate Duplicate Conditional Fragments

The same fragment of code is in all branches of a conditional expression.
Move it outside the expression.

###5. Replace Condtional with Polymorphism
Motivation
One of the grandest sounding words in object jargon is polymorphism. The essence of polymorphsim is that it allows you to avoid writing an explicit con- ditional when you have objects whose behavior varies depending on their types.
As a result you find that case statements or if-then-else statements that switch on type codes are much less common in an Object-Oriented program.


Polymorphism gives you many advantages. The biggest gain occurs when this same set of conditions appears in many places in the program. If you want to add a new type, you have to find and update all the conditionals. But with polymorphism you just create a new class and provide the appropriate methods. Clients of the class don’t need to know about the polymorphism, which reduces the dependencies in your system and makes it easier to update.


Ruby’s duck typing makes it easy to introduce polymorphism. In a statically typed language like Java, you need inheritance or implementation of an inter- face to be able to call a method polymorphically. But in Ruby, an object’s cur- rent set of methods—not its inheritance from a particular class—determines its valid semantics. So long as objects A and B have the same method, you can call them in the same way.


##Mechanics
Polymorphism in Ruby can be achieved in a couple of ways. In its simplest form, you can implement the same method signature on multiple objects and call these methods polymorphically. You can introduce a module hierarchy and have the method that is to be called polymorphically on the module. Or you can introduce an inheritance hierarchy and have the method that is to be called polymorphically on the subclasses. In each case, the mechanics are the same.
The code you target may be a case statement or an if statement.

**1. If the conditional statement is one part of a larger method, take apart the
conditional statement and use Extract Method.
**2. If necessary use Move Method to place the conditional at the appropriate place in the object structure.
**3. Pick one of the polymorphic objects. Create a method on the polymor- phic object that will override the conditional statement method. Copy the body of that leg of the conditional statement into the polymorphic method and adjust it to fit.


###6. Introuduce a Null Object
You have repeated checks for a nil value.
Replace the nil value with a null object.

The essence of polymorphism is that instead of asking an object what type it is and then invoking some behavior based on the answer, you just invoke the behavior. The object, depending on its type, does the right thing. One of the less intuitive places to do this is where you have a null value in a field.

