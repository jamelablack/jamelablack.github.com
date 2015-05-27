---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 6: Mailgun"
tags: tealeaf
---
##Mailgun
Mailgun is an email service for developers - which is clear in its interface. It has a ver low-level design that is easy for tweaking and has alot of options.

* It focuses on transaction emails and it optimized for deliverability.
* It also allows for the receipt of emails.
* You can specify an email address that you want mailgun to listen. Mailgun can pre-process the message and parse out parts of the email and send to your app for processing.
* It can handle campaign emails, number of clicks, etc.


##How to Integrate Mailgun into our Server
We are using Heroku and there is a [Mailgun/Heroku add-on](https://addons.heroku.com/mailgun). There is a free option that limits to 300 emails/day.

It's pretty simple beyond that. You just log into Heroku, select your app, and run the command:
{%highlight ruby%}
heroku addons:create mailgun
{%endhighlight%}

###Read Documentation on Sending Emails via SMTP
Go to your configuation for your production env, and change the setting to those listed. Make sure you keep stmp as the delivery method. Env variables will be set by Heroku, so you don't have to worry about that.

Mailgun supports two delivery methods. The reason we can use smtp is becasue ActionMailer will help us to prepare to proper map format. But HTTP is also an option. You don't have to rely on Rails to do that. You can just use REST client that will use an HTTP POST. Check the API documentation to see what other headers can be sent.

