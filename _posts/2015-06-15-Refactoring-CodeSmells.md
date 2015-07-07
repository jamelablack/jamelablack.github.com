---
layout: post
title: "Refactoring - Code Smells"
tags: refactoring
---

##Code Smells

###1. Duplicated Code
Extract Method
Extract Module or Extract Class if you have duplicated code in two unrelated classes.

###2. Long Method
Shorten Method with Extract Method
If you have a method with too many parameters that when you extract the method, you are passing in too many variables and creating so many temps that the original method was more readable, you can often use Replace Temp with Query or Replace Temp with Chain to eliminate the temps. Long lists of parameters can be slimmed down with Introduce Parameter Object and Preserve Whole Object. If you’ve tried that, and you still have too many temps and parameters, it’s time to get out the heavy artillery: Replace Method with Method Object.

Conditionals and loops also give signs for extractions. Use Decompose Conditional to deal with conditional expressions. Replace loops with Collection Closure Methods and consider using Extract Method on the call to the closure method and the closure itself.

###3. Long Parameter List
Use Replace Parameter with Method when you can get the data in one parameter by making a request of an object you already know about. Use Pre- serve Whole Object to take a bunch of data gleaned from an object and replace it with the object itself. If you have several data items with no logical object, use Introduce Parameter Object to clump them together, or Introduce Named Parameter to improve the fluency.


###4. Divergent Change
Divergent change occurs when one class is commonly changed in different ways for different reasons. If you look at a class and say, “Well, I will have to change these three methods every time I get a new database; I have to change these four methods every time there is a new financial instrument,” you likely have a situation in which two objects are better than one.


To clean this up you identify everything that changes for a particular cause and use Extract Class to put them all together.


###5. Shotgun Surgery
Divergent change is one class that suffers many kinds of changes, and shotgun surgery is one change that alters many classes.


In this case you want to use Move Method and Move Field to put all the changes into a single class. If no current class looks like a good candidate, create one. Often you can use Inline Class to bring a whole bunch of behavior together. You get a small dose of divergent change, but you can easily deal with that.

###6. Feature Envy
The most common focus of the envy is the data. We’ve lost count of the times we’ve seen a method that invokes half a dozen getting methods on another object to calculate some value. Fortunately the cure is obvious, the method clearly wants to be elsewhere, so you use Move Method to get it there. The fun- damental rule of thumb is to put things together that change together. Data and the behavior that references that data usually change together, but there are exceptions. When the exceptions occur, we move the behavior to keep changes in one place.


###7. Data Clumps
Often you’ll see the same three or four data items together in many places: instance variables in a couple of classes, and parameters in many method signatures. Bunches of data that hang around together really ought to be made into their own object. The first step is to look for where the clumps appear as instance variables. Use Extract Class on the instance variables to turn the clumps into an object. Then turn your attention to method signatures using Introduce Parameter Object or Preserve Whole Object to slim them down.


A good test is to consider deleting one of the data values: If you did this, would the others make any sense? If they don’t, it’s a sure sign that you have an object that’s dying to be born.

###8. Primitive Obsession
Most programming environments have two kinds of data. Record types allow you to structure data into meaningful groups. Primitive types are your building blocks. Records always carry a certain amount of overhead: They may mean tables in a database, or they may be awkward to create when you want them for only one or two things.

You can move out of the cave into the centrally heated world of objects by using Replace Data Value with Object on individual data values. If you have conditionals that depend on a type code, use Replace Type Code with Polymorphism, Replace Type Code with Module Extension, or Replace Type Code with State/Strategy.


If you have a group of instance variables that should go together, use Extract Class. If you see these primitives in parameter lists, try a civilizing dose of Intro- duce Parameter Object. If you find yourself picking apart an array, use Replace Array with Object.


###9. Temporary Field
Sometimes you see an object in which an instance variable is set only in cer- tain circumstances.
Use Extract Class to create a home for the poor orphan variables. Put all the code that concerns the variables into the component. You may also be able to eliminate conditional code by using Introduce Null Object to create an alterna- tive component for when the variables aren’t valid.


###10. Message Chain
You see message chains when a client asks one object for another object, which the client then asks for yet another object, which the client then asks for yet another object, and so on. You may see these as a long line of get_this methods, or as a sequence of temps. Navigating this way means the client is coupled to the structure of the navigation. Any change to the intermediate relationships causes the client to have to change.


The move to use here is Hide Delegate. In principle you can apply Hide Delegate to potentially every object in the chain, but doing this often turns every intermediate object into a middle man. Often a better alternative is to see what the resulting object is used for. See whether you can use Extract Method to take a piece of the code that uses it and then Move Method to push it down the chain. If several clients of one of the objects in the chain want to navigate the rest of the way, add a method to do that.


Some people consider any method chain to be a terrible thing. We are known for our calm, reasoned moderation. Well, at least in this case we are.

###11. Middle Man
One of the prime features of objects is encapsulation—hiding internal details from the rest of the world. Encapsulation often comes with delegation. You look at a class’s interface and find half the methods are delegating to this other class. After a while it is time to use Remove Middle Man and talk to the object that really knows what’s going on. If only a few methods aren’t doing much, use Inline Method to inline them into the caller. If there is additional behavior, you can use Replace Delegation with Hierarchy to turn the real object into a module and include it in the middle man. That allows you to extend behavior without chasing all that delegation.


###12. Inappropriate Intimacy
Sometimes classes become far too intimate and spend too much time delving into each other’s private parts. We may not be prudes when it comes to people, but we think our classes should follow strict, puritan rules.

Overly intimate classes need to be broken up as lovers were in ancient days. Use Move Method and Move Field to separate the pieces to reduce the intimacy. See whether you can arrange a Change Bidirectional Association to Unidirec- tional. If the classes do have common interests, use Extract Class to put the commonality in a safe place and make honest classes of them. Or use Hide Del- egate to let another class act as go-between.

###13. Alternative Classes with Different Interfaces
Use Rename Method on any methods that do the same thing but have different signatures for what they do. Often this doesn’t go far enough. In these cases the classes aren’t yet doing enough. Keep using Move Method to move behavior to the classes until the protocols are the same.


###14. Impossible Library Class
Reuse is often touted as the purpose of objects. We think reuse is overrated (we just use). However, Ruby’s open classes make this easy to fix using Move Method to move the behavior needed directly to the library class.


###15.  Data Class
These are classes that have attributes, and nothing else. Such classes are dumb data holders and are almost certainly being manipulated in far too much detail by other classes. Use Remove Setting Method on any instance variable that should not be changed. If you have collection instance variables, check to see whether they are properly encapsulated and apply Encapsulate Collection if they aren’t.


Look for where these getting and setting methods are used by other classes. Try to use Move Method to move behavior into the data class. If you can’t move a whole method, use Extract Method to create a method that can be moved. After a while you can start using Hide Method on the getters and setters.
Data classes are like children. They are okay as a starting point, but to par- ticipate as a grownup object, they need to take some responsibility.

###16. Refused Bequest
Subclasses get to inherit the methods and data of their parents. But what if they don’t want or need what they are given? They are given all these great gifts and pick just a few to play with.


The traditional story is that this means the hierarchy is wrong. You need to create a new sibling class and use Push Down Method to push all the unused methods to the sibling. That way the parent holds only what is common.

###17. Comments
Don’t worry, we aren’t saying that people shouldn’t write comments. In our olfactory analogy, comments aren’t a bad smell; indeed they are a sweet smell. The reason we mention comments here is that comments often are used as a deodorant. It’s surprising how often you look at thickly com
Tip When you feel the need to write a comment, first try to refactor the code so that any comment becomes superfluous.


###16. Metaprogramming Madness
While in most cases Ruby’s dynamic nature provides great benefits, it can be misused. Some metaprogramming techniques can result in obfuscated code. The method_missing hook, for example, often results in code that is difficult to under- stand. It can be a powerful tool if an object’s interface cannot be determined at coding time, but unless it’s absolutely necessary I use Replace Dynamic Receptor with Dynamic Method Definition or even a simple Extract Method to remove the method_missing definition. If the method_missing definition is truly needed, I might use Isolate Dynamic Receptor to separate concerns.


###17. Disjointed API
Libraries are often written with flexibility as the number one priority. The author needs to build in this flexibility so that her library can be used by many different people in many different ways. This flexibility often presents itself as a relatively fine-grained, disjointed API, with many configuration options.


More often than not, an individual project will not take advantage of all the configuration options. The same configuration options will be used over and over. If this is the case, use Introduce Gateway to interact with the API in a simplified way.


Introduce Expression Builder can be applied to both internal and external APIs to interact with the public interface in a more fluent manner.


###18. Repetitive Boilerplate
One of the easiest ways to remove duplication is Extract Method. Extract the method and call it from multiple places. Some kinds of methods become so commonplace that we can go even further. Take for example attr_reader in Ruby. Implementing attribute readers is so common in object-oriented languages that the author of Ruby decided to provide a succinct way to declare them. Intro- duce Class Annotation involves annotating a class by calling a class method from the class definition in the same way that attr_reader is called. Most code isn’t simple enough to declare in this way, but when the purpose of the code can be captured clearly in a declarative statement, Introduce Class Annotation can clarify the intention of your code.
