---
layout: post
title: "POODR - Ch1: Object Oriented Design"
tags: tealeaf
---
Object Oriented Design requires that you shift from thinking about the world as a collection of predefined procedures to modeling th world as a series of messages that are past between objects.

##In Praise of Design
Desgin really doesn't become a concern if nothing ever changes (requirements). But something always changes and it is the need for change that makes desugn matter.

###Why is Change Hard?
Object oriented applications are made of partst that interact to produce the behavior of the whole. The parts are objects, interactions are emobodied in the messages that pass between them.

OOD is about managing dependencies. It's a set of coding techniques that arrange dependencies such that objects can tolerate change. Changing one object forces change upon its collabortors.

However when objects know too much, they have expectations about the world in which they reside. And these expectations contstrain them.

##A Practical Definition of Design
Design is an art of arranging code. Design has two components:
You must not only write code for the feature you plan to deliver today, you must also create code that is amendable to being changed later.

Design that is that anticipates specific future requirements almost always ends badly. It shouldn't guess hte future, but rather perserves options for accomodating the future, leaving you romm to move.

Primary goal is to allow you to desiglm later, and its primary goal is reduce the cost of change.

###Design Principles
SOLID stands for Single Responsibilty, Open-Closed, Liskov Substitution, Interface Segregation, & Dependancy Inversion

DRY - Don't Repeat Yourself

LoD - Law of Demeter

###Design Patterns
give us the power to name common problems and solve those problems in common ways, allowing us to big fuzzy things into focus.

###The Act of Design
How design fails:
1. Lack of it - programmers typically now little about design
2. Under-designed - lack of knowledge of design principles
3. Over-design - seasoned devs may disgn relentlessly
4. Object Oriented Desgin fails when the act of design is seperated from the act of programming. The feedback loop should be timely and incremental; the iterative techniques of the Agile Development are perfect for well-designd OO applications.

###When to Design
Agile believes that the mot cost-effective way to produce what customers's really want is to collaborate with the, building software one bit at atime such that each deivered bit has the opprotunity to alter ideas about the next.

There is no need to do BUFD (Big Upfront Design)

The Agile Process gaurantees change and yu ability to do so depneds on your design (sinple, flexibe, and malleable code).

###Judging Design
Back in the day, design was jusged by the number of lines of code (sometimes referred to as SLOC). The ultimate software metric would be cost per feature over the time interval the matters.

Failure to design increases your technical debt.

Your decision about design depends on two things.:
your skills and your timeframe.

##A brief introduction to Object Oriented Programming:
OO applications are made of objects and messages that pass between them.

###Procedural Langauges
is a non-object oriented progamming technique. Every possible data type and po]eration already exists, you can't make up wholly new operations or brand new data types.

##Object Oriented Programming
Ruby has a string object instead of a string data type. Because Ruby string objects supply their own operations, Ruby doesn't have to know anything in particular about the string data type but rather it only need to know how to send object messages.

Ruby allows to you to provide classes tha essentially blueprints for the consturction of similar objects. A class defines methods (definitions of behavior) and attributes(definitons of variables), while also allowing you to instantiate new objects with same operations or behavior.



