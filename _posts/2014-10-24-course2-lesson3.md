---
layout: post
title: "Rapid Prototyping with Rails: Lesson 3, part 2"
tags: tealeaf
---
##Items Covererd:
1. Creating/Updating User
2. Login/Logout
3. Memoization


##Creating/Updating User
1. Create resources
    resources :users, only: [:create]
2. Create users_controller.rb (new, create, edit, update actions)
    - since :password is the virtual attribute for :password_digest, you have to include it in strong_params:
{%highlight ruby%}
def users_params
  params.require(:user).permit(:username, :password)
end
{%endhighlight%}

3. Create  users folder under views (new.html.erb, edit.html.erb, _form.html.erb)
4. Add password validations
{%highlight ruby%}
validates :password, presence: true, on: :create, length: {minimun: 3}
{%endhighlight%}
5. Change users#new to register under routes file to add a register path '/register':
{%highlight ruby%}
    get '/register', to: 'users#new', as: 'register'
{%endhighlight%}
6. Add register link to _navigation.html.erb
{%highlight ruby%}
        <li>
          <%= link_to 'Register', register_path %>
{%endhighlight%}

##Login/Logout
1. Create routes - *resources are reserved for models, typcially custom routes are on-offs
{%highlight ruby%}
      get '/login', to: 'sessions#new'
      post '/login', to: 'sessions#create'
      get '/logout', to: 'sessions#destroy'
{%endhighlight%}
2. Create sessions_controller.rb
{%highlight ruby%}
class SessionsController < Application.rb
  def new
  end

  def create
    Let's write pseudo code:
    What are we trying to do... we want to run user.authenticate('password')
    How do we accomplish this?
    1. Get user object
    2. See if the password matches
    3. If so, log in
    4. If not, error message

    user = User.find_by(username: params[:username])
    # we can use a local variable bc we aren't using model backed forms so persistency isn't required.

    if user && user.authenticate(params[:password])
      session[:user_id] = user_id
      flash[:notice] = "You're logged in!"
      redirect_to root_path

      #never pass an object into sessions ( like @user for example)
      bc sessions only have 4KBs in bandwidth and will generate a "cookie overload error" after the user carries out
      so many actions.

    else
      flash[:error] = "There was something wrong with your username and password"
      #Avoid being too specific here, (i.e. found username but wrong password)... to prevent hacking
      redirect_to register_path
    end
  end


  #Login pages should be https://, if not, passwords submitted over an http:// server are not encrypted. The https://
  is not a very high added expense. You just have to purchase SSL certificate.


  def delete
    session[:user_id] = nil
    flash[:notice] = "You've logged out"
    redirect_to root_path
    end
  end
  {%endhighlight%}

3. Create a sessions folder under views, new.html.erb file

4. Add login/logout link to nav bar
{%highlight ruby%}
#_navigation.html.erb

    if logged_in?
      <li>Hi<%= current_user.username %></li>
  <li>
      <%= link_to "Log Out", logout_path %>
  </li>
    <% else %>
    <li>
      <%= link_to "Register", register_path %>
    </li>
    <li>
      <%= link_to "Log in", login_path %>
    <% end %>
    </li>
{%endhighlight%}

5. We need to create a method called logged_in (referenced above) in the application_controller.rb bc we want this method accessible across the application.
{%highlight ruby%}

application_controller.rb

helper_method :current_user, :logged_in?

def current_user
  #Wpseudo-code
  #If current user, return the user obj
  #else return nil
    @current_user ||= User.find(session[user_id]) if session[:user_id]
  end
end

  def logged_in?
    !!current_user
  end
end
{%endhighlight%}

##Memoization
For effecient performance, we want to hit the database only once per request.
For methods that are being called multiple times, it is more effecient to save in an instance variable rather than
continously hitting the database.


6. We can now hide links based on whether a user is logged in or not. Let's hide the 'edit' link
{%highlight ruby%}
#posts/index.html.erb
<h3> Welcome </h3>
 <ul>
  <% @posts.each do |post| %>
  <li>
  <%= link_to post.title, post_path(post)
  <% if logged_in? %>
  [<%= link_to 'Edit', edit_post_path(post) %>]
{%endhighlight%}


7. We also want to make sure you can't go to the link via url
{%highlight ruby%}
#posts_controller.rb
  before_action :require_user,except: [:index, :show]
{%endhighlight%}

8. We must create a require_user method
{%highlight ruby%}
#application_controller.rb

  def require_user
    if !logged_in?
      flash[:notice] = "Must be logged in to do that"
      redirect_to root_path
  end
{%endhighlight%}
9. Set up before_action under the comments_controller.rb, also.
{%highlight ruby%}
#comments_controller.rb
class CommentsController < ApplicationController.rb
before_action :require_user

  def create
    @comment.creator = current_user #change from @comment_creator = User.first
  end
end
{%endhighlight%}
10. Define post.creator as current_user
{%highlight ruby%}
#posts_controller.rb
  def create
    @post.creator = current_user
  end
{%endhighlight%}

Remember - when you want to lock down a peice of functionality, you have to
1. Remove it from the user interface (links)
2. Set up a before_action


