---
layout: post
title: "Refactoring - Composing Methods"
tags: refactoring
---

###Extract Method
is moving duplicate code into a method. Create a new method, and name it after the intention of the method (name it by what it does, not by how it does it). 

###Inline Method
is replacing the caller of the method with the body of the method

###Inline Temp
Find all references to the temp and replace them with the right-hand side of the assignment. 
###Replace Temp with Query
Find all references to the temp and replace them with the right-hand side of the assignment. 

###Replace Temp with Chain
removes unneeded creations of temp (variable assignments for method calling expressions) and chain with the right side of the expression, most likely using .self

####Introduce Explaining Variable
You have a complicated expression, Put the result of the expression, or parts of the expression, in a temporary variable with a name that explains the purpose.

###Split Temporary Variable
You have a temporary variable assigned to more than once, but it is not a loop variable nor a collecting temporary variable.
Make a separate temporary variable for each assignment.

###Remove Assignments to Parameters: The code assigns to a parameter. Use a temporary variable instead.

###Replace Method with Method Object
You have a long method that uses local variables in such a way that you cannot apply Extract Method. Create a new class, name it after the method. 

###Extract Surrounding Methods
Step 1: Use Extract Method on one piece of duplication. Name it after the duplicated behavior.
This will become our surrounding method.
For now the surrounding method will still perform the unique behavior.


Step 2:  Modify the calling method to pass a block to the surrounding method. Copy the unique logic from the surrounding method into the block. 


Step 3:  Replace the unique logic in the extracted method with the yield keyword. 


Step 4:  Identify any variables in the surrounding method that are needed by the unique logic and pass them as parameters in the call to yield. 

###Introduce the Named Parameter
The parameters in a method call cannot easily be deduced from the name of the method you are calling. Convert the parameter list into a Hash, and use the keys of the Hash as names for the parameters.

###Dynamic Method Definition
####Example: Using def_each to Define Similar Methods
Defining several similar methods is verbose and often unnecessary. For example, each of the following methods is simply calling the state method.

###Isolate Dynamic Receptor
A class utilizing method_missing has become painful to alter.
Introduce a new class and move the method_missing logic to that class.


###Move Eval from Runtime to Parse Time
You need to use eval but want to limit the number of times eval is necessary. Move the use of eval from within the method definition to defining the method
itself.













