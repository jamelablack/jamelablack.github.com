---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 6: Securing Access"
tags: tealeaf
---
##Securing Access for Different Actors
In past, to secure access we would have created a before filter for each controller and specified the action - however, this can become cumbersome depending on the size of the app.

Instead, we can have our Admin::TodoController < AdminsController and have AdminsController < ApplicationController, moving our before filter from  Admin::TodoController to AdminsContollers

{%highlight ruby%}
class AdminController < ApplicationController
  before_filter :ensure_admin
  def ensure_admin
    flash[:error] = 'You don't have access to this"
    redirect_to root_path unless current_user.admin?
  end
end
{%endhighlight%}

##AuthenticatedController
We can even take things one step further by creating an AuthenticatedController that can superclass the TodosController and AdminsController.

We need to ensure the user is logged in first:
{%highlight ruby%}
#app/controllers/authenticated_controller.rb
class AuthenticatedController < ApplicationController
  before_filter :ensure_sign_in
end
{%endhighlight%}

##Actor-Specific Controllers
It's very common to have several types to actors within an application and features specific to the public. To manage this, it's best to have actor-specfic controllers and then subclass one another. This allows you to compartentalize actions for actors easily.
