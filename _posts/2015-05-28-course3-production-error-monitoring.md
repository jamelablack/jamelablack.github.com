---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 6: Simple Deployment Pipeline"
tags: tealeaf
---

##Set-up Production Error Monitoring

Regardless how well we have tested our application, there will always be errors on the production server. To get notified of production errors so you can respond to them, you need to set up an error monitoring service in production.

Install [Sentry](https://getsentry.com/welcome/).

You also need to install the ["sentry-raven"](https://github.com/getsentry/raven-ruby) gem in your app.

###Setup
After creating an account online and adding your project to Sentry, you will be given a DSN that should go into your config/initializers folder.
{%highlight ruby%}
# in Rails, this might be in config/initializers/sentry.rb
Raven.configure do |config|
  config.dsn = 'http://public:secret@example.com/project-id'
end
{%endhighlight%}
