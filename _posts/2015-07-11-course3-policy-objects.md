---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 8: Beyond MVC - Policy Objects"
tags: tealeaf
---

##Policy Objects
Do you have complicated code in your logic - cure that baby with Policy Objects!
Policy Objects can be used to represent the state of an object:

{%highlight ruby%}

#apps/controllers/todos_controller.rb
def create
  @todo = Todo.new(params[:todo)]
  if @todo.save_with_tags
    user.created_at < Date.new(2013, 1, 1) || user.plan.premium?
    new_credit_balance = current_user.current_credit_balance - 1
  else
    new_credit_balance = current_user.current_credit_balance - 2
  end
{%endhighlight%}


{%highlight ruby%}
#app/models/user_level_policy
class UserLevelPolicy
attr_reader :user

def initialize(user)
  @user = user
end


def premuim?
  user.created_at < Date.new(2013, 1, 1) || user.plan.premium?
end

def silver?
end

def bronze?
end
{%endhighlight%}
