---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 3: Part 2"
tags: tealeaf
---
##Items Covered:
1. Structural Refactor
2. Skinny Controller, Fat Models
3. Rspec Macros
4. Rspec Shared Examples

##Structural Refactor
Controllers are more of traffic control and methods containing logic should refarctored to the model level

- For simple refactors such as moving methods from controllers to methods,
since your test suite is already comprehensive enough to cover the current model, you dont have to create additional tests at the model level, only if you decide to add additional functionality.

 Typically you shouldn't have more than one conditional or return from a method within a method. You should refactor by moving a piece of the code to private method.


##Skinny Controller, Fat Models
One of the most common refactors in Rails and one of the most well-known architectural principals in rails.

Fat Models, means the models assume the most responsibilty.
Good blog post to read: [Skinny Models, Fat Controllers](http://weblog.jamisbuck.org/2006/10/18/skinny-controller-fat-model)
Here - James talks about moving responsibilty from the view, to the controller, and then to the model.

Examples of Opensource Project Management Tools that have need to go on a Fat Controller Diet:
[ChilliProject](https://github.com/chiliproject/chiliproject)
[Redmine](https://github.com/edavis10/redmine)



###Refactoring to fat models in MyFlix
Tip: When refactoring your controllers to fat models, do not move methods that take parameters in a form
because that is the purpose of the controller - to talk to the views and models.
Look for those that iterate through the model.

##Rspec Macros
Allows you to extract logic that can be used by multiple rspec controllers.

So rather than the following at the begining of each controller test:
{%highlight ruby%}
before do
  frank = Fabricate(:user)
  session[:user_id] = frank.id
end
{%endhighlight%}

We could create a macro, spec/support/macros.rb and include the above code:
{%highlight ruby%}
def set_current_user
  frank = Fabricate(:user)
    session[:user_id] = frank.id
  end

def current_user
  User.find(session[user_id])
end
{%endhighlight%}

AND in your test:

{%highlight ruby%}
before {set_current_user}
{%endhighlight%}

Now, if I want to clear the current user to test the path of an unauthorized user
create another macro called clear_current_user, make it the first line in your test.
{%highlight ruby%}
def clear_current_user
  session[user_id] = nil
end
{%endhighlight%}

##Shared Examples

What bout you want to test the redirect for unauthenticated users for not just index but index and new

Create a Shared Example:
{%highlight ruby%}
#spec/support/shares_examples.rb
shared_examples "require_sign_in" do
  it "redirects to the front page" do
    response.should redirect_to root_path
  end
end
{%endhighlight%}

In your test: you can't just write:
{%highlight ruby%}
  it behaves_like "require_sign_in"
{%endhighlight%}

because you have to clear the current user and hit the action first:
{%highlight ruby%}
context "user not signed in" do
  before do
    clear_current_user
    get :index
  end
  it behaves_like "require_sign_in"
end
{%endhighlight%}
But this is frustrating becasue although we DRY'd up our code a little,
we still have to add a before block for the clear_current_user and the index.


It would be great if there's a way to pass the action to the shared example, and there is:

{%highlight ruby%}
shared_examples "require_sign_in" do
  it "redirects to the front page" do
    clear_current_user
    action
    response.should redirect_to root_path
  end
end
{%endhighlight%}
 AND in your test:

{%highlight ruby%}
 context "not_signed_in" do
    it_behaves_like "require_sign_in" do
    let(:action) { get :index }
  end
end
{%endhighlight%}
AND you actually don't need the context anymore:
{%highlight ruby%}
<index>
it_behaves_like "require_sign_in" do
  let(:action) { get :index }
end

<new>
it_behaves_like "require_sign_in" do
  let(:action) { get :new }
end

<create>
it_behaves_like "require_sign_in" do
  let(:action) { post :create, todo: {name: 'something'} }
end
{%endhighlight%}

Shared examples can be used on both controller and model levels
For more info:

[Shared Examples Docs](https://www.relishapp.com/rspec/rspec-core/docs/example-groups/shared-examples)
