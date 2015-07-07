---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 6: Mailgun & Heroku"
tags: tealeaf
---

##To send emails from your Heroku app using Mailgun is simple:

###1. Mailgun Addon
Add the [Mailgun](https://addons.heroku.com/mailgun) add-on to your Heroku app through the command line (within your app project directory)

{%highlight ruby%}
heroku addons:create mailgun
{%endhighlight%}

or you can add via their web interface

![Mailgun Heroku Image](jamelablack.github.io/images/mailgun.png "Mailgun addon")

###2. Change your smtp settings
Then you copy and paste their smtp settings into your config/production.rb file. Make sure to change your domain name and add your default url options (which should point to your domain name) - if not, you will get an error: Errno::ECONNREFUSED: Connection refused - connect(2) for action mailer ...port(25)
{%highlight ruby%}
config.action_mailer.delivery_method = :smtp
  # SMTP settings for mailgun
  config.action_mailer.smtp_settings = {
    :port           => ENV['MAILGUN_SMTP_PORT'],
    :address        => ENV['MAILGUN_SMTP_SERVER'],
    :user_name      => ENV['MAILGUN_SMTP_LOGIN'],
    :password       => ENV['MAILGUN_SMTP_PASSWORD'],
    :domain         => 'aqueous-coast-5067.herokuapp.com',
    :authentication => :plain,
  }
  config.action_mailer.default_url_options = { :host => 'aqueous-coast-5067.herokuapp.com' }

  {%endhighlight%}

###3. Heroku config
You do not have to store the ENV variable in application.yml, you only have to run "heroku config" from your command line.

###4. If you find you do need to store ENV variable in your app, check out Figaro.
