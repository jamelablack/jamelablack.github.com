---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 8: Beyond MVC - Domain Objects"
tags: tealeaf
---
##Domain Objects
Domain Objects are objects that are within the domain object model. Not all of them inherit from active record though, often times they do not always map to database tables.

For important attributes, it may be worth it to create a seperate domain object rather than calling an object to access that attribute.

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

  current_user.current_credit_balance = new_credit_balance
  current_user.save

  if new_credit_balance < 0
    AppMailer.notify.insufficient_funds
  elsif current_user.current_credit_balance < 10
    AppMailer.notify_low_balance
  end
    redirect_to root_path
  else
    render :new
  end
{%endhighlight%}



Let's create a domain called Credit:

{%highlight ruby%}
#app/models/credit.rb

class Credit
  attr_accessor :credit_balance, :user

  def initialize(user)
    @credit_balance = user.current_credit_balance
    @user = user
  end

  def -(number)
    credit_balance = credit_balance - number
  end

  def save
    user.current_credit_balance = credit_balance
    user.save
  end

  def depleted?
    credit_balance < 0
  end

  def low_balance?
    credit_balance < 10
  end
end
{%endhighlight%}

Now let's update the Todos Controller:

{%highlight ruby%}
#apps/controllers/todos_controller.rb
def create
  @todo = Todo.new(params[:todo)]
  credit = Credit.new(current_user)

  if @todo.save_with_tags
    if UserLevelPoicy.new(ccurent_user).premium?
    credit = credit - 1
  else
    credit = credit - 2
  end

  credit.save

  if credit.depleted? < 0
    AppMailer.notify.insufficient_funds
  elsif credit.low_balance?
    AppMailer.notify_low_balance
  end
    redirect_to root_path
  else
    render :new
  end
{%endhighlight%}
