---
layout: post
title: "Rapid Prototyping with Rails: Lesson 2, part 2"
tags: tealeaf
---

#Topics Covered:
1. Validation
2. Errors
2. General Create Pattern
3. Update Path
4. Form Partial
5. Before_action
6. Nested Resources

##Validations
are added always at the model layer:
{%highlight ruby%}
class Post < ActiveRecord::Base
  has_many :comments
  validates :title, :presence true
end
{%endhighlight%}

Now if you go into the rails console and do Post.new without a title, an error will
not occur until you try to hit the database, so either Post.save or Post.create.

When you try to do so, the console will return "False". This is because, there is
an if clause in the create method. See below. Running post.errors in your console will
print out an array called ActiveModel Errors and will tell you all fields that require
validation... in other words.. can't be blank.
{%highlight ruby%}
def create
  @post = Post.new(post_params)

  if @post.save
    flash[:notice] = "Your post was created."
    redirect_to posts_path
  else
    render 'new'
  end
end
{%endhighlight%}

##Errors
post.errors will generate an array. If you want to parse out the messages...
type post.errors.full_messages

If post.save/post.create is unsuccessful, then you will 'render' the new form.
You will 'render' and not 'redirect' becasue we need to access the errors on the instance
variable in order to display the errors. When the validations arent met and it fails to save,
save, false is returned in the console and the errors array is populated. However, as
it stands, the new form will be rendered.

How to display the Errors (Rails Backed Forms Only)
{%highlight ruby%}
<h3> Creating a Post </h3>
<% if @post.errors.any? %>

  <h5> There were some errors: </h5>

  <ul>
    <% @post.errors.full_messages.each do |msg|
    <div class='alert alert-error'>
        <li><% msg %> </li>
      <% end %>
    </div>
  </ul>
<% end %>
{%endhighlight%}

{%highlight ruby%}
<h4> Rails model-backed form helpers </h4>
  <%= form_for @post do |f| %> # @post could also be Post.new
  <%= f.label :title %>
  <%= f.text_field :title %>

  <%= f.label :url %>
  <% f.text_field :title %>
  <br/>
  <%= f.submit "Create Post", class: 'btn btn-primary' %>
<% end %>
{%endhighlight%}

###Field with Errors
Rails creates a div class to allow you to easily point out or style your fields with
errors called "div class = fields with errors"

Form_for does alot of guessing
It guesses that New forms should go to /'posts'

If you:
{%highlight ruby%}
<%= form_for @post do |f| %> # @post could also be Post.new
{%endhighlight%}
If you:
{%highlight ruby%}
  <%= form_for Post.first do |f| %> # Will bring up the first Post object
{%endhighlight%}

  When doing so, that Post.first is calling
  method='POST' with a route of '/posts/id' - which is not a listed route, therefore
  should not be valid.

  But instead of erroring out, it is going to the post/update controller action.

  Why?

  This is bc the in addition to the Utf8 and the authenticity token, there is hidden
  method called _method = 'patch'

  _method is what Rails uses to determine which HTTP verb is used in the routes file
  or input method. This is because not every browser supports all HTTP methods, just most likely GET and POST.

  1. If the object is an existing object, Rails will automatically give it an
  _method = "PATCH" and the route will become '/post/id'

  2. The new post form and the edit post form can be identical because _method in form_for will take care of it.

  3. Now you can create a partial to extract the form code that will be used for both
  new and update partials

  So now:
    1. Create a partial called _form.html.erb under posts
    2. Extract common code
{%highlight ruby%}
      _form.html.erb
        <% if @post.errors.any? %>
          <h5> There were some errors: </h5>
          <ul>
            <% @post.errors.full_messages.each do |msg|
            <div class='alert alert-error'>
                <li><% msg %> </li>
              <% end %>
            </div>
          </ul>
        <% end %>
{%endhighlight%}

{%highlight ruby%}
    <h4> Rails model-backed form helpers </h4>
      <%= form_for @post do |f| %> # @post could also be Post.new
      <%= f.label :title %>
      <%= f.text_field :title %>

      <%= f.label :url %>
      <%= f.text_field :title %>
      <br/>
      <%= f.submit(@post.new_record? ? "Create Post" : "Update Post", class:
      'btn btn-primary' %>
    <% end %>
{%endhighlight%}

  3. Then update new_html.erb
{%highlight ruby%}
  <h3> Creating a Post </h3>
  <%= render 'form' %>
{%endhighlight%}

  4. Then do the same for edit.html.erb
{%highlight ruby%}
  <h3> Edit a Post </h3>
  <%= render 'form' %>
{%endhighlight%}

      BUT you must also ensure that you have all your necessary instance variables
      created so in your controller

{%highlight ruby%}
      def edit
        @post = Post.find(params[:id])
      end
{%endhighlight%}

      which will route to 'post/:id/edit'... named route edit_post_path(post)

##UPDATE
{%highlight ruby%}
  def update
    @post = Post.find(params[:id])

    if @post.update(post_params)
      flash[:notice] = "The post has been updated"
      redirect_to posts_path
    else
      render :edit
    end
  end
{%endhighlight%}

Remember since your _form.html.erb is being used by four actions:
new, create, edit, and update,
So you have to make sure you have @post defined in each action in your controller
or an exception will be thrown. Consequences of dry.

##Before_action
For Rails 4, its before_action
For Rails 3, its before_filter

Since we are using @post = Post.find(params[:id])
several times in our controller by various actions (show, edit, and update), we should create a method called set_post and pass it the before_action, then we can remove @post from those

{%highlight ruby%}

class PostController < ApplicationController
before_action :set_post, only: [:show, :edit, :update]

  def show
  end

  def edit
  end

  def update
  end

  def set_post
    @post = Post.find(params[:id])
  end
end
{%endhighlight%}

We use before_actions for two reasons
1. Set up an instance variable
2. Set up a redirect based on some condition (authentication)

##Nested Resources
###Nested Routes
* Redirect => URL
* Render => template

How to create a comment on the Show Post page:

###Routes File:
{%highlight ruby%}
resources :posts, except: [:destroy] do
  resources :comments, only: [:create]
end
{%endhighlight%}


{%highlight ruby%}
show.html.erb (show posts page)
<h5> Create a Comment </h5>

  <%= form_for [@post, @ comment] %>
    <%= f.text_area :body %>
    <br/>
      <%= f.submit "Create Comment" class: "btn btn-primary" %>
  <% end %>
{%endhighlight%}


'/posts/3/comments'
* We are trying to @post must be an existing post and @comment has to be an existing
comment.

{%highlight ruby%}
PostsController.rb
def show
  @comment = Comment.new
end
{%endhighlight%}

###Create a CommentsController
{%highlight ruby%}

comments_controller.rb

class CommentsController < ApplicationController

def create
  @post = Post.new(params[:post_id]) #Always run params in binding.pry (in your controller) to check the attribute name. Nested routes always have the parent route appended to the id, therefore it would be 'post_id'

  @comment = Comment.new(params.require(:comment).permit(:body))

  #we could also associate comment and post
      @comment.post = @post.comments
      or
      you could change the above line for @ comments to create an in memory post.comment
      object with that has the post_id already filled out.
        @comment = @post.comments.build(params.require(:comment).permit(:body))
  if @comment.save
    flash[:notice] = "Your comment was added"
    redirect_to post_path(@post)
  else
    render 'posts/show' #must be a template file... you are trying to indicate
                        where the file is located
  end
end

#typially, we would just create a seperate method for comment_params, we aren't doing
that because we aren't trying to reuse this line of code for the Update action, only create.
end
{%endhighlight%}

##CRUD
CRUD is an acronym that stands for "create", "retrieve", "update" and "delete". You'll hear many developers say something like "we just need a CRUD web interface for tickets". This is what Rails gives us out of the box: an ability to perform basic CRUD actions on a resource as a web application. Below is how the default Rails actions map to CRUD:

* Create: new, create
* Retrieve: index, show
* Update: edit, update
* Delete: destroy

##Render vs Redirect

You should also understand what a render is vs a redirect. Render compiles the template
into HTML and sends the HTML back as part of the response. Redirect sends back a URL
as part of the response; there's no HTML in a redirect. Most browsers follow the redirected
URL automatically, and a new request is issued. All redirects will eventually lead to rendering of some template, otherwise your browser will display a "too many redirects" error.


Why does the URL stay at /posts when there's a validation error? Shouldn't it be /posts/new?

The request URL is what's showing up in the address bar, not the response.
The response is processed by your browser. The request URL is shown in the address bar.

In the case of a new post form submission, the request URL is /posts.
This has nothing to do with the response sent back. The URL only changes on successful
post creation because in that case the response is a redirect, and your browser issues a
new request, which changes the address bar.
