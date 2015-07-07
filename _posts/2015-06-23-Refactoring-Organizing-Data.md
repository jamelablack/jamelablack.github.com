---
layout: post
title: "Refactoring - Organizng Data"
tags: refactoring
---
###1. Self Encapsulated Field
You are accessing a field directly, but the coupling to the field is becoming awkward.
Create getting and setting methods for the field and use only those to access the field.


###2. Replace Data Value with Object
You have a data item that needs additional data or behavior.
Turn the data item into an object:
**1. Create the class for the value. Give it an equivalent field to the field in the source class. Add an attribute reader and a constructor that takes the field as an argument.
**2. Change the attribute reader in the source class to call the reader in the new class.

###3. Change Value to Reference
You have a class with many equal instances that you want to replace with a single object.
Turn the object into a reference object.

###4. Change Reference to Value
You have a reference object that is small, immutable, and awkward to manage. Turn it into a value object.

###5. Replace Array with Object
You have an Array in which certain elements mean different things. Replace the Array with an object that has a field for each element.
**1. Create a new class to represent the information in the Array. Give it a method called [] so that callers that read the Array can be changed one by one. Give it a method called []= so that callers that write to the Array can be changed one by one.
**2. Construct the new object wherever the Array was instantiated.

###6. Replace Hash with Object

You have a Hash that stores several different types of objects, and is passed around and used for more than one purpose.
Replace the Hash with an object that has a field for each key.

**1. Create a new class to represent the information in the Hash. Give it a method called [] so that callers that read the Hash can be changed one by one. Give it a method called []= so that callers that write to the Hash can be changed one by one.
**2. Construct the new object wherever the Hash was instantiated.



