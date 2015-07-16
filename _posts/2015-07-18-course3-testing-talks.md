---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 8: - Mocks & Stubs"
tags: tealeaf
---
##Magic Tricks of Testing

We want unit tests to be
*Thorough
*Stable
*Fast
*Few

Focus on Messages
Objects are simple-minded.

Message Origin
Objects under test have three message origin options:
*1. Incoming Messages
*2. Outgoing Messages
*3. Messages sent to self (messages that are completely contained inside the object and are unknown to the outside)

Message Type
*1. Query - returns something, changes nothing (addition method)
*2. Command - returns something, changes something (save method)

We often times conflate commands and queries (like .(pop))

Rules of the Unit Test Minimalist
INCOMING QUERY MESSAGES

1. Test Incoming Query Messages by making assetions about what the send back. Makes an assertion about a value (state). Here we want to test the interface and not the implementation. If we just care about what value is returned and not how we get there... then I can change the code without breaking it.


INCOMING COMMAND MESSAGES
2. Test incoming command messages by making assertions about direct public side effects. Direct means its the responsibility of the last ruby class invloved. You're making an assertion on the new value as result of calling that command.

Key Takeway - The receiver of incoming messages has the sole responsibility of asserting the result direct public side effect. This is the sole place where we are making an assertions about values, testing the value of the thing returned


MESSAGES SENT TO SELF

Private Methods - Don't test private methods. Don't make assertiond about their result. Do not expect to send them. Doing so will add no value to your tests and will make it impossible to change in the future wihthout breaking something. They also discourage other devs from touching your code so you may want to add the comment "if these tests fail, delete them."

Caveat: Break rule if itk saves $$$ during development.

Key Takeway - Ignore them.


OUTGOING QUERY MESSAGES
Making assertions on query messages is the job of the reciever. Therefore making an assertion on outgoing messages is redundant and should be avoided. It makes your tests are break when there is a change and it is very combersome to understand what going on. It adds no value.

Rule: Do not test outgoing query messages. Do not make assertions about their result. Do not epxect to send them.

You will notice that both tests made to self and tech on outgoing query messages have the same rule, don't do them. This is because they have no visbible side effects, they are invisible to the rest of your application and their tests add no value.

Rule: If a message has no visible side-effects, the sender should not test it.

OUTGOING COMMAND MESSAGES
Outgoing command messages must be sent, they have to be sent or the app will not be correct. Testing, however the distant side effects of sending the message is an integration test. Asserting that the state of a thing changed in the database is not job of the outgoing command message. It's job is ensure that a message is sent. Asserting that some change has occured or making an assertion about the state of the DB will forever connect you to that result and the objects.. even those distant involved.

Testing that a message was sent requires a mock. Testing this way makes a bet that the edge (the API...the message sent) is more stable than the distant side effect and the path to it.

Rule: Test outgoing command messages by setting expectations on them being sent. Caveat: Break Rule if ide effects are stable and cheap... and close. The further away - the worse.

Rule: It is our job to ensure that mocks honor their contract. Ensure test doubles stay insync with the API

Gems that help you manage your mocks:
*Bogus
*Quacky
*Rspec-Fire
*Minitest-Firemock



