---
layout: post
title: "Refactoring - Moving Features from Objects"
tags: refactoring
---
The most fundamental question in object oriented design is "Where to put responsibilities" Often times issues around these questions resolve these problems simply by using Move Method and Move Field to move the behavior around. If we need to use both, use Move Field first and then Move Method.

###Move Method
A method is, or will be, using or used by more features of another class than the class on which it is defined.
Create a new method with a similar body in the class it uses most. Either turn the old method into a simple delegation, or remove it altogether. Examine all features used by the source method that are defined on the
source class. Consider whether they also should be moved.

###Move Field

A field is, or will be, used by another class more than the class on which it is defined.
Create a new attribute reader (and if necessary, a writer) in the target class, and change all its users.

###Extract Class
You have one class doing work that should be done by two. Create a new class and move the relevant fields and methods from the old class into the new class.

###Inline Class
A class isn’t doing very much.
Move all its features into another class and delete it.

###Hide Delegate
A client is calling a delegate class of an object.
Create methods on the server to hide the delegate. One of the keys, if not the key, to objects is encapsulation. Encapsulation means that objects need to know less about other parts of the system. Then when things change, fewer objects need to be told about the change—which makes the change easier to make.

###Remove the Middle Man

A class is doing too much simple delegation.
Get the client to call the delegate directly. In the motivation for Hide Delegate, we talked about the advantages of encapsulating the use of a delegated object. There is a price for this. The price is that every time the client wants to use a new feature of the delegate, you have to add a simple delegating method to the server. After adding features for a while, it becomes painful. The server class is just a middle man, and perhaps it’s time for the client to call the delegate directly.


