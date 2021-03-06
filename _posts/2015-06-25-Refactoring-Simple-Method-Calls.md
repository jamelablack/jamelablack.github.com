---
layout: post
title: "Refactoring - Simple Method Calls"
tags: refactoring
---
###1.. Rename Method
The name of a method does not reveal its purpose.
Change the name of the method.
###2. Add Parameter
A method needs more information from its caller.
Add a parameter for an object that can pass on this information.

###3. Remove Parameter
A parameter is no longer used by the method body.
Remove it.
###4. Seperate Query from Modifier
You have a method that returns a value and also changes the state of an object.
Create two methods, one for the query and one for the modification.

###4. Parameterize Method
You have a method that returns a value and also changes the state of an object.
Create two methods, one for the query and one for the modification.

Motivation
You may see a couple of methods that do similar things but vary depending on a few values. In this case you can simplify matters by replacing the separate methods with a single method that handles the variations by parameters. Such a change removes duplicate code and increases flexibility, because you can deal with other variations by adding parameters.

###5. Perserve Whole Object
You are getting several values from an object and passing these values as param- eters in a method call.
Send the whole object instead.

###6. Replace Parameter with Method
An object invokes a method, then passes the result as a parameter for a method. The receiver can also invoke this method.
Remove the parameter and let the receiver invoke the method.


###7. Introduce Parameter Object
You have a group of parameters that naturally go together.
Replace them with an object.


###8. Remove Setting Method
A field should be set at creation time and never altered.
Remove any setting method for that field.
Providing a setting method indicates that a field may be changed. If you don’t want that field to change once the object is created, don’t provide a setting method. That way your intention is clear and you often remove the possibility that the field will change.

###9. Hide Method
A method is not used by any other class.
Make the method private.


###10. Replace Constructor with Factory Method
You want to do more than simple construction when you create an object.
Replace the constructor with a factory method.
The most obvious motivation for Replace Constructor with Factory Method is when you have conditional logic to determine the kind of object to create. If you need to do this conditional logic in more than one place, it’s time for a Factory Method.

###11.Replace Error Code with Exception
A method returns a special code to indicate an error.
Raise an exception instead.


###12. Replace Exception with Test
You are raising an exception on a condition the caller could have checked first.
Change the caller to make the test first.
Exceptions are an important advance in programming languages. They allow us to avoid complex codes by use of Replace Error Code with Exception. Like so many pleasures, exceptions can be used to excess, and they cease to be pleasur- able. Exceptions should be used for exceptional behavior: behavior that is an unexpected error. They should not act as a substitute for conditional tests. If you can reasonably expect the caller to check the condition before calling the operation, you should provide a test, and the caller should use it.

###13. Introduce Gateway
You want to interact with a complex API of an external system or resource in a simplified way.
Introduce a Gateway that encapsulates access to an external system or resource.

###14. Introduce Expression Builder
You want to interact with a public interface in a more fluent manner and not muddy the interface of an existing object.
Introduce an Expression Builder and create an interface specific to your application.
￼Introduce Expression Builder
￼￼


