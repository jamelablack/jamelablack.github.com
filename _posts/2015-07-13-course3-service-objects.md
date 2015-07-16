---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 8: Beyond MVC - Service Objects"
tags: tealeaf
---

##Service Objects
Just like we can extract domain logic into domain objects, we can extract business level logic into service objects.  Currently, the below process is modeling the Credit Deduction Business Process. Let's extract that into a Service Object called CreditDeduction.

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

###Create the Service Policy called CreditDeduction

{%highlight ruby%}
#app/services/credit_deduction.rb
Class CreditDeduction


end
{%endhighlight%}

####Extract business process related Code
{%highlight ruby%}

#app/services/credit_deduction.rb
Class CreditDeduction
  attr_accessor :user

  def initialize(user)
    @user = user
  end

  def deduct_credit
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
  end
end
{%endhighlight%}

#### Update the TodoController

{%highlight ruby%}
#apps/controllers/todos_controller
def create
  @todo = Todo.new(params[:todo)]
  credit = Credit.new(current_user)

  if @todo.save_with_tags
    CreditDeduction.new(credit, current_user).deduct_credit

    redirect_to root_path
  else
    render :new
    end
  end
end
{%endhighlight%}

#### Move instatiating a new Credit Object to your Service Policy
{%highlight ruby%}

#app/services/credit_deduction.rb
Class CreditDeduction
  attr_accessor :credit, :user

  def initialize(credit, user)
    @credit = Credit.new(user)
    @user = user
  end

  def deduct_credit
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
  end
end
{%endhighlight%}

{%highlight ruby%}

#apps/controllers/todos_controller
def create
  @todo = Todo.new(params[:todo)]

  if @todo.save_with_tags
    CreditDeduction.new(current_user).deduct_credit

    redirect_to root_path
  else
    render :new
    end
  end
end
{%endhighlight%}














