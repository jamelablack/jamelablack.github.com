---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 8: Beyond MVC - Object Composition, Object Oriented Design, & YAGNI"
tags: tealeaf
---
Rails provides an MVC framework - but there is a point where the complexity of your application grows beyong this structure. Typically, when this first happens, devs move the logic from their controllers, to the models (skinny contollers/fat models) - but often we find that we are pushing too much logic into the models, making them bloated. Often times models at the center of your application, that are so large (in complexity and touches so many other objects) that people are afraid to touch it (becasue something bad will happen if you mess up) is called God Objects (God Models).

##Object Composition
We can take some of the responsibility from those objects however, this does come at a cost.

####In-direction
When you extract logic to various objects within a process, it can be difficult to chase down what is happening as the process is not all in one view. Well defined names and interfaces.

However - there are some ways to abuse object composition.

###YAGNI (You Aren't Going to Need It)
is over-extracting or abstracting to a layer that is not useful at the moment, in hopes that it will be in the future, but you prolly won't. It is better to wait as you will never know less about the future than you do now.



