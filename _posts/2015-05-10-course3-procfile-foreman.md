  ---
  layout: post
  title: "Build Robust & Production Quality Applications - Lesson 6: Procfile & Foreman"
  tags: tealeaf
  ---
On your server, you can run both web and worker processes locally - you can also do the same on Heroku, identifiying the processes you would like to run in your Procfile. A procfile is placed in the root of your diretory and declare what types of processes you want run in your app.

In your procfile, you will define:
{%highlight ruby%}
<process type>: <command>
web: bundle exec rails server -p $PORT
worker: bundle exec rake jobs:work (example from delayed jobs)
worker: sidekiq
{%endhighlight%}

##Develop locally with Foreman
Instead of having to start both Sidekiq and our Rails server locally, we can use format to initiate both processes by runniong "foreman start" in your app directory.

##Heroku
will parse out the web processes from your Procfile.
{%highlight ruby%}
heroku ps
{%endhighlight%}

will list the processes running

{%highlight ruby%}
heroku logs
{%endhighlight%}
will list all process types

Heroku will allow you to run one web & worker processes but if you'd like to run more, it may costs.
