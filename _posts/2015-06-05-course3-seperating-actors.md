---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 6: Continuous Delivery"
tags: tealeaf
---
##Actors
Actors are different types of users, most likely referred to as admins, becasue they act differently within your application.

In some instances, you could:
{%highlight ruby%}
def index
  if current_user.admin?
    Todo.all
    else ....
  end
{%endhighlight%}

But dependeing on the size, this could be quite cumbersome.

Another alternative, is we can create a seperate action:
{%highlight ruby%}
def admin_index
  @todos = Todo.all
end
{%endhighlight%}

The advatange is that everything the admin can do is seperate from the other actors. But this is also cumbersome b/c if we have multiple actors, before each action, we have to check the users role.

##Namespace
So instead, we are going to work with Namespaces.
{%highlight ruby%}
#routes.rb
resources :todos, only: [:index, :create, :new, :show]
namespace :admin do
  resources :todos, only: [:index]
end
{%endhighlight%}

The above route would give us:
{%highlight ruby%}
http://localhost:3000/admin/todos
{%endhighlight%}

So, therefore your controller would be in app/controllers/admin/todos_controller, rather than app/controllers/todos_controller

Add when created, our class name is prefixed with the module Admin
{%highlight ruby%}
class Admin::TodosController < ApplicationController
end
{%endhighlight%}

We also need to run a migration to add the admin column to the users table
{%highlight ruby%}

rails g migration add_admin_to_users
{%endhighlight%}

{%highlight ruby%}
class AddAdminToUsers < ActiveRecord::Migration
  def change
    add_column :users, :admin, :boolean
  end
end
{%endhighlight%}

Our viewv template in this case would be in:
{%highlight ruby%}
app/views/admin/index.html.haml
{%endhighlight%}

This layout allows us to add things like an admin dashboard
