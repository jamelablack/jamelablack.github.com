---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 1: Rational for Testing"
tags: tealeaf
---
##Why Do we test our software?

Here, we are referring to automated testing:
What would be our alternatives:
###Option 1. No tests at all:
this doesn't exist. This means that customers will do the testing for you. All software has bugs, so bugs encountered by your customers will make them aggravated and then you will lose them as customers, support costs will sky rocket

###Option 2. Rely on QA Team - When we have a clear division b/w development
Typically for larger companies. Code is thrown over the wall to the QA team according feature scope or some other arbitrary milestone, for example.
 * The QA Team writes design test cases according to code base and file bugs with the dev team.
 * The code is authorized for release once everything is fixed.

 This could create:
####1. Organizational Trust Problems
 communication b/w the teams typically is not very good. Including finger pointing, etc.
####2. QA Team
* nobody likes them and they get blamed for everything. When everything works, devs take the credit
  and when they don't work, the QA Team is blamed because their purpose is to catch things like that.
* Also, when deadlines, ship dates are missed, the dev team will often blame the QA Team for all the bugs or requirements
  they have to work through. They complain that they don't have time to work on features bc of this.
* No one really aspires to be good "testers", typically its junior devs and there is a high-turnover bc no one typically wants to
  stay there... therefore knowledge is lost.
* Release Cycles - typically both teams are competing for the same time bc each wants to a good job. This pushes back the deadlines.

###Impact of having not automated tests
1. Code quality - QA Team, developers, stakeholders, customers don't care as much
2. Isolated Knowledge - only the developer can maintain that code, sometimes a form of job security
3. How do you know the implemented feature works
4. How do you know that things did not break as a result of this new feature - called regression problems
