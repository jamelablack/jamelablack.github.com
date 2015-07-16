---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 8: Beyond MVC - Decorators"
tags: tealeaf
---
Let's say we have a Todo model. The methods name_only? and display_text is purely presentational logic.

{%highlight ruby%}
Class Todo < ActiveRecord::Base
  has_many :taggings
  has_many :tags, through :taggigngs

  def name_only?
    description.blank?
  end

  def display_text
    name + tag_text
  end

  private
  def tag_text
    #some complicated regex code
  end
{%endhighlight%}

We decide to move those two methods to a decorator:
{%highlight ruby%}
#app/decorators/todo_decorator.rb

Class TodoDecorator
  attr_reader :todo
  def initialize(todo)
    @todo = todo
  end

  def display_text
    todo.name + tag_text
  end

  private
  def tag_text
  if todo.tags.any?
    #some complicated regex code
  end
end
{%endhighlight%}

Now we can remove the extracted code from the Todo model and update the way its called in the views:

{%highlight ruby%}
#app/views/todos/new.html/haml
%ul
  - @todos.each.do |f|
    %li
      = link_to TodoDecorator.new(todo).display_text, todo
{%endhighlight%}

If we find ourselves using the decorator alot, we could create a decorator method on the Todo model:
{%highlight ruby%}
#app/models/todo.rb

def decorator
  TodoDecorator.new(self)
end
{%endhighlight%}


We may in the future also want to call methods on the decorator and the original model and we don't want the views to be concerned with this level of logic (when to instantiate the decorator vs the model)

In the Todos controller, we could pass an array of decorators in addition to an array of Todos:

{%highlight ruby%}
#app/controllers/todos_controller.rb
def index
  @todos = current_user.todos.map(&:decorator)
end
{%endhighlight%}

But then our decorator has to respond to the methods within the decorator and within the model itself. To allow this we must extend  Ruby the Forwardable module and create a delegator method:

{%highlight ruby%}
#app/decorators/todo_decorator.rb

Class TodoDecorator

  extend Forwardable
  def delegators :todo, :name_only?

  attr_reader :todo
  def initialize(todo)
    @todo = todo
  end

  def display_text
    todo.name + tag_text
  end

  private
  def tag_text
  if todo.tags.any?
    #some complicated regex code
  end
end
{%endhighlight%}

Delegators are valuable when we present models one way in the database and differently in the browser - we use decorators to bridge this gap. When we seperate the domain logic from presentation logic, we can also test them seperately, creating decorator specs.

##Draper
If you like decorators, you should check out [Draper](http://github.com/drapergem/draper). Instead adding extendeding the Fowardable module, etc. All you have to do is create the decorator, make sure add 'delegae_all' and update the videos controller:
{%highlight ruby%}
class VideoDecorator < Draper::Decorator
  delegate_all

  def rating
    object.rating.present? ? "#{object.rating}/5.0" : "N/A"
  end
end
{%endhighlight%}


{%highlight ruby%}
class VideosController < ApplicationController
  before_filter :require_user

  def index
    @videos = Video.all
    @categories = Category.all
  end

  def show
    @video = VideoDecorator.decorate(Video.find(params[:id]))
    @reviews = @video.reviews
  end
{%endhighlight%}



