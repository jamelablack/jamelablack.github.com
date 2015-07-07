---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 6: Email Service Providers"
tags: tealeaf
---
With Gmail, there are clear sending [limitations](https://support.google.com/a/answer/166852?hl=en).

Sending out emails is something that you should offload onto a third-party provider as it is a critical business process. You want to ensure emails are recieved right away and not in the recipients email folder.

Mailgun & Postmark are the recommended email service providers. When considering vendors, you want to focus on devliverability.

Check out the is [Quora article](http://www.quora.com/Why-are-Mailgun-and-Postmark-so-much-more-expensive-than-Sendgrid-and-AWS-SES) on comparing well-known email service providers. In this course, we will use Mailgun.

Good [blogpost](http://www.gotealeaf.com/blog/handling-emails-in-rails) for getting set up with Gmail.
