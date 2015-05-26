---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 1: Front Page"
tags: tealeaf
---
##Front Page
Front Pages or Root/home pages are typically static images. Those
are rendered using the pages#controller. In this case however, we do not need the
pages#front action defined in the controller bc it doesnt do anything.
For now, we can just have the empty controller bc due rails conventions, Rails will just render
the view of the same name if the action is not present in the controller.
{%highlight ruby%}
#pages_controller.rb
class PagesController < ApplicationController
  def front
    redirect_to home_path if current_user
  end
end
{%endhighlight%}
