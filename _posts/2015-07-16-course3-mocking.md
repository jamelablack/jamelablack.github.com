---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 8: - Mocking"
tags: tealeaf
---
In previous lessons, extracted some of our logic into policy, domain and service objects. We can now update our TodosController spec to reflect these new objects, but we don't have to test the functionality of the service level object, for example - we just to make sure that the object can respond to the actions extracted to the Service Level process. We can accomplish this with mocking & stubbing. Ensuring that a message has been sent and the object can respond to that message. We are not concerned with the response for that message.


So far with stubbing (which is all we have done up until this point), we have always been asserting on values or the state of the system. With mocking, we asserting on communication. Mocks will always be in unit testing becasue you ar stubbing out your collaborators. If you have to create too many mocks, then you know that you are missing another layer of abstraction. Limiting your spec to focusing on one object allows you code smells like these.

Picking up from our last example, we would create a mock with the following. Please note that the trigger is after the assertion:

{%highlight ruby%}
it "delegates to CreditDeduction to deduct credit" do
  credit_deduction = double("credit deduction")
  CreditDeduction.stub(:new).(alice).and_return(credit_deduction)

  expect(credit_deduction).to_recieve(:deduct_credit)
  post :create, todo: {name: "cook", description: "I like cooking!" }

end
{%endhighlight%}






