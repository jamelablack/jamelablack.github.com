---
layout: post
title: "Rapid Prototyping with Rails: Lesson 2, part 1"
tags: tealeaf
---
Items covered:
1. Rails Forms
2. Strong Parameters


##Rails Forms
There are 3 ways to create a form
  1. Pure HTML
  2. Rails Forms Helpers
  3. Rails model-backed Forms Helpers

1. PURE HTML
{%highlight ruby%}
<h5> Pure HTML Form </h5>
  <form action='/posts' method='POST'>
    Title <input type='text' name='my_title'>

    <br/>

    <input type='submit' class='btn btn-primary'>
  </form>
{%endhighlight%}

*Rails has something called CSFP - cross site forgery protection built into it.
It tries to prevent fake forms from submitting to your website.. without token.*
This can be disabled in your application_controller.rb

In the above section, where it says
{%highlight ruby%}
  Title <input type='text name='my_title'>
{%endhighlight%}

Giving the title attribute a name called "my_title" gives you the ability refer to it as a hash. So params typed into pry would give you:
{%highlight ruby%}
  {"my_title" => "some title"}
{%endhighlight%}
or
{%highlight ruby%}
  params[:my_title] would give you "some title" returned
{%endhighlight%}

*Hash with indifferent access - means although the key is a string, I can refer to it as a :symbol"
So therefore, in your console, you could type:
{%highlight ruby%}
  Post.create(title: params[:my_title]) **This is called mass assignment**
{%endhighlight%}

2. RAILS FORMS HELPERS
When creating pure html forms, use form helpers.
{%highlight ruby%}
<h4> Rails Forms Helpers </h4>
<%= form_tag '/posts' do %>
  <%= label_tag :title, "Input a title" %>
  <%= text_field_tag :title %>
  <br/>
  <%= submit_tag "Create Post", class: 'btn btn-primary' %>
<% end %>
{%endhighlight%}


##Form helpers, do several things:
  1. Automatically determines the method='POST'
    B/c it is a form
  2. Security
    Provides an UTF 8 & authenticity token in a hidden div that helps allows us to submit under cross site forgery protection
  3. Using the label_tag (helper)
    Gives more guidance to the blind. Label elements correalate with input elements and is read
    by screenreaders for the hearing impaired. <Where is says "Input a title">


3. RAILS MODEL-BACKED FORMS
{%highlight ruby%}
<h4> Rails model-backed form helpers </h4>
  <%= form_for @post do |f| %>
  <%= f.label :title %>
  <%= f.text_field :title %>

  <%= f.label :url %>
  <% f.text_field :title %>
  <br/>
  <%= f.submit "Create Post", class: 'btn btn-primary' %>
<% end %>
{%endhighlight%}

You can mix and match forms and tags (from 2 and 3), if needed.

###What is happening here:
The form_for is taking an object (@post). We must define @post in our controller:
{%highlight ruby%}
    def new
      @post = Post.new
    end
{%endhighlight%}

###This gives us a few liberties:
{%highlight ruby%}
Post.create(params[:post]) or Post.new(params[:post]) is effectively doing mass assignment,
using the hash assigned to (params[:post])
{%endhighlight%}

##Associations
{%highlight ruby%}
has_many :comments
has_many :posts
{%endhighlight%}

These become getters and setter bc they are virtual attributes as opposed to the
actual column names which are actual attributes

When you have a model-backed form, you must use fields that are virtual or actual attributes
because your input will attempt roll up and mass assign. This will not work if the actual
attribute does not exist.
{%highlight ruby%}
  f.label :whatever (yes)
  f.text_field :whatever (no)
{%endhighlight%}

However, the above can be accomplished with Rails Forms Helpers (#2) or Pure HTML,
you can do what you want and just parse it out on the back-end.
{%highlight ruby%}
      URL
      name = (post[:url]) is how rails magic, mass assignment occurs
      In console or during binding.pry, typing params will list
      UTf8
      Authenticity Token
      Post attributes in hash form
      Commit
      Action
      Controller
{%endhighlight%}

Typing params[:post] is the same as post.
{%highlight ruby%}
Strong Parameters
def create
  post = Post.new(params[:post])
end
{%endhighlight%}

Will through an error in Rails 4 bc you don't have strong_parameters.
They realized that the concern regarding strong parameters and security really
didn't occur at the model layer but rather in the action based on user access,
roles, etc.

So no-longer a white list of attributes available for mass_assignment,
attr_accessible :name :sex :location

So the Rails 4 version is:
{%highlight ruby%}
def create
  @post = Post.new(post_params)
end

private
def post_params #because we want to reuse this, so lets create a post_params method
  params.require(:post).permit(:title, :url)
end
{%endhighlight%}

OR you can assign strong parameters based roles

{%highlight ruby%}
def post_params
  if user.admin?
    params.require(:post).permit! #permit everything

  else
    params.require(:post).permit(:title, :url) #only permit title and url
  end
end
{%endhighlight%}

If in the future, you add other input fields but do not update your strong params, it will just wipe away
your entry attempt, it will not throw an error as to let hackers know what ways to pass the security restriction

So Rails 3 is attr_accessible :title (whitelist) and Rails 4 is strong_params in controller action.

Now that you have understood post params, next memorize the following:

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
