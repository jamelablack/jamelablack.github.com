---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 6: Riding Unicorns"
tags: tealeaf
---
##Using Unicorn as a Production Server
Visit http://devcenter.heroku.com/articles/rails-unicorn

The issue with the rails server, is that it only allows for one web process at a time. Unicorn allows you to add additional servers, which can be specified in your config/unicorn.rb file

###1. Add the Unicorn gem to your gemfile
{%highlight ruby%}
gem 'unicorn'
{%endhighlight%}
 then, run bundle install

###2. Config
This is where you define the number worker processes. Worker processes in this context is all processes not just background processes. Below indicates that we want three worker processes running at once.
{%highlight ruby%}
#config/unicorn.rb
worker_processes Integer(ENV["WEB_CONCURRENCY"] || 3)
timeout 15
preload_app true

{%endhighlight%}

### Update your Procfile
Set Unicorn as the server for the web process in your Procfile:

{%highlight ruby%}
web: bundle exec unicorn -p $PORT -c ./config/unicorn.rb
{%endhighlight%}

##Adding Unicorn to your Heroku App

###1. Add Redis to go

heroku addons:create redistogo

###2. Run the following for Sidekiq version 3.0 or later:
heroku config:set REDIS_PROVIDER=REDISTOGO_URL

###3 [Free background job on Heroku](https://coderwall.com/p/fprnhg/free-background-jobs-on-heroku)
Running more than 1 dyno (server) on Heroku costs, and ain't nobody got time for that. Coderwall has created a hack:


