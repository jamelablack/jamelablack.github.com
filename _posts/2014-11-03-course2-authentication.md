---
layout: post
title: "Rapid Prototyping with Rails: Lesson 3, Authentication"
tags: tealeaf
---

##Steps

###Step 1: Create routes
{%highlight ruby%}
#routes.rb
add sessions#new, sessions#create, sessions#destory routes
{%endhighlight%}

###Step 2: Create sessions template
{%highlight ruby%}
#sessions/new.html.erb
Add html
{%endhighlight%}

###Step 3: Sessions_Controller.rb
{%highlight ruby%}
class SessionsController < ApplicationController
  def create
  user = User.where(username: params[:username]).first
    or
  user = User.find_by(username: params[:username])

Alternative Flash syntax

  flash.now[:error] = "There is something wrong with your username or password."
  render :new
  end
end
{%endhighlight%}

###Step 4: Helpers
{%highlight ruby%}
#application_helper.rb
  1. create current_user and logged_in? methods

* Memoization: allows you to only hit the database once when being called multiple times on a template

  2. add helper_method syntax to make accessible in views
{%endhighlight%}

###Step 5. _navigation.html.erb
Two ways to protect the fuctionality of your application for non-logged in users:
1. Links on User Interface (using 'if logged_in? method')
2. Urls/Controller actions (using before_action)

We want to keep those who aren't logged in from creating a post.
{%highlight ruby%}
<% if logged_in? %>
<div class='nav item'>
  <%= link_to "New Post", new_post_path, class: 'btn btn-success btn-small' %>
</div>
<% end %>
{%endhighlight%}

###Step 6. Add before action to prevent navigation to certain links
{%highlight ruby%}
posts_controller.rb
  add before_action :require_user, except: [:index, :show]
{%endhighlight%}

###Step 7. require_user method
{%highlight ruby%}
Create require_user method in application_controller.rb
{%endhighlight%}
###Step 8. Add current_user
{%highlight ruby%}
Remove User.first and add current_user for post.creator and comment.creator
{%endhighlight%}
