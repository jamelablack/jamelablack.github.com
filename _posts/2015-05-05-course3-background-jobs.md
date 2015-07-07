---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 6: Background Jobs"
tags: tealeaf
---
##Background Jobs
Email sending typically is not very fast since we have to rely on some third party application. Waiting can slow up your app and potentially frustrate users so it's best to put non-essential and non-time-sensitive tasks in background jobs.

Since you can web processes and background job processes running locally, you need tp run both the rails server and sidekiq servers to process jobs respectively.

###Resque
came out of Github - Resque is our Redis-backed library for creating background jobs, placing those jobs on multiple queues, and processing them later.

Benefits: has admin interface, is powered by Github
Delayed Job does not require Redis, both uses a pulling queue
Beanstalk and Stalker may be better if you care about speed, as they have a pulling mechanism


Sidekiq is a higher performance background solution than Resque and can even be ported, added on top of resque solution.

Check out the Railscast on Resque for intro.

###Sidekiq
In order to use Sidekiq, you must install and run [Redis](http://redis.io). Once downloaded, run the "$ src/redis-server" in the redis-3.0.2 folder.

You must also gem install sidekiq, and run "bundle exec sidekiq"

When watching the Railscast on Sidekiq, it talks about the creation of workers:

{%highlight ruby%}
#app/controllers/invitations_controller.rb
class InvitationsController < ApplicationController
  before_filter :require_user
  def new
    @invitation = Invitation.new
  end

  def create
    @invitation = Invitation.create(invitation_params.merge!(inviter_id: current_user.id))

    if @invitation.save
      InivtationWorker.perform_async
      flash[:success] = "You've successfully invited #{@invitation.recipient_name}."
      redirect_to new_invitation_path
    else
      flash[:error] = "Please fill in all inputs."
      render :new
    end
  end
{%endhighlight%}

{%highlight ruby%}
#app/workers
class InvitationWorker
  include Sidekiq::Worker

  def perform(invitation_id)
    invitation = Invitation.find(invitation_id)
    AppMailer.send_invitation_email(invitation).deliver
  end
end
{%endhighlight%}

But instead of creating workers, we can just use [delayed extentions](https://github.com/mperham/sidekiq/wiki/Delayed-extensions#actionmailer)
{%highlight ruby%}
#app/controlers/invitations_controller.rb
  def create
    @invitation = Invitation.create(invitation_params.merge!(inviter_id: current_user.id))

    if @invitation.save
      AppMailer.delay.send_invitation_email(@invitation.id)
      flash[:success] = "You've successfully invited #{@invitation.recipient_name}."
      redirect_to new_invitation_path
    else
      flash[:error] = "Please fill in all inputs."
      render :new
    end
  end
{%endhighlight%}

Since we aren't running .perform_async on a worker, we need to pass @invitiation.id to the AppMailer:

{%highlight ruby%}
#app/app_mailer.rb
def send_invitation_email(invitation_id)
    @invitation = Invitation.find(invitation_id)
    mail to: @invitation.recipient_email, from: "info@myflix.com", subject: "Invitation to join Myflix"
  end
end
{%endhighlight%}
To run tests, you have to add the following to run them [inline](https://github.com/mperham/sidekiq/wiki/Testing):
{%highlight ruby%}
#spec/spec_helper.rb

require 'sidekiq/testing'
Sidekiq::Testing.inline!
{%endhighlight%}
