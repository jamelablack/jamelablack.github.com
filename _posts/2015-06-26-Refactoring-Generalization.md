---
layout: post
title: "Refactoring - Dealing wihth Generalization"
tags: refactoring
---

###1. Pull Up Method
You have methods with identical results on subclasses. Move them to the superclass.

###2.Push Down Method
Behavior on a superclass is relevant only for some of its subclasses. Move it to those subclasses

###3. Extract Module
You have duplicated behavior in two or more classes.
Create a new module and move the relevant behavior from the old class into the module, and include the module in the class.


###4. Inline Module
The resultant indirection of the included module is no longer worth the duplication it is preventing.
Merge the module into the including class.

###5. Extract Subclass
A class has features that are used only in some instances.
Create a subclass for that subset of features.

###6. Introduce Inheritance
You have two classes with similar features.
Make one of the classes a superclass and move the common features to the superclass.


###7. Collapse Hierarchy
A superclass and subclass (or module and the class that includes the module) are not very different.
￼￼Merge them together

###8. Form Template Method
You have two methods in subclasses that perform similar steps in the same order, yet the steps are different.
Get the steps into methods with the same signature, so that the original methods become the same. Then you can pull them up.

###9. Replace Inheritance with Delegation
A subclass uses only part of a superclass interface or does not want to inherit data.
Create a field for the superclass, adjust methods to delegate to the superclass, and remove the subclassing.
￼Replace Inheritance with Delegation

###10. Replace Abstract Superclass with Module
You have an inheritance hierarchy, but never intend to explicitly instantiate an instance of the superclass.
Replace the superclass with a module to better communicate your intention.

