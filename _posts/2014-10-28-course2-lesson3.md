---
layout: post
title: "Rapid Prototyping with Rails: Lesson 3, part 4"
tags: tealeaf
---
###1. Create vote controller action
{%highlight ruby%}
 class PostsController < ApplicationController
  def vote
    binding.pry
  end
 end
 {%endhighlight%}

 Entering params in rails console, will show us that we passing an id parameter but not the
 boolean to indicate true/false, whether the post is up or down.

###2. Add boolean parameter to views to create link
Add boolean parameter to views to create link  /posts/2/vote?vote=true&param2=2
{%highlight ruby%}
 #posts/index.html.erb
 <div class='row'>
    <div class='span0 well text-center'>
      <%= link_to vote_post_path(post, vote: true), method: 'post' do %>
{%endhighlight%}

###3. Now that we have our thing we are voting on and our vote, we can create our vote action
{%highlight ruby%}
class PostsController < ApplicationController
  before_action :set_post, only: [:show, edit, :update, :vote]

def vote
  #post = Post.find(params[:id]) or use @post(created in the before_action)
  @vote = Vote.create(voteable: @post, creator: current_user, vote: params[:vote])

  if @vote.valid?
    flash[:notice] = "Your vote was recorded"
  else
    flash[:error] = "Your vote was not recorded"
  end
    redirect_to :back (to go back to the original page viewed)
  end
end
{%endhighlight%}

###4. Wire up the same for the down vote in the views/posts/index
{%highlight ruby%}
<div class='row'>
  <div class='span0 well text-center'>
    <%= link_to vote_post_path(post, vote: false), method: 'post' do %>
{%endhighlight%}


###5. What if I want to display the number of votes
{%highlight ruby%}
  <%post.votes.size%>
{%endhighlight%}

###6. What if I want the number of votes to decrease when pressing the down button.
Although this may seem like a presentational concern that would require a helper
method - however storing the number of votes is a data concern and not a presentational one.
Method regarding business logic belong in the model itself.
{%highlight ruby%}
 class Post < ActiveRecord::Base
   def total_votes
    up_votes - down_votes
   end

   def up_votes
    self.votes.where(vote: true).size
   end

   def down_votes
    self.votes.where(vote: false).size
   end
 end
{%endhighlight%}
 So now in the presentation layer, we can call total_votes:
 {%highlight ruby%}
 <% post.total_votes %>
 {%endhighlight%}

###7. What if we wanted to sort
{%highlight ruby%}
class PostsController < ApplicationController

  def index
    @posts = Post.all(|x| x.total_votes).reverse
  end
end
{%endhighlight%}

###8. How can we make sure that each user only votes once for post:

There are many ways to accomplish this but the best way would be to add the
uniqueness validation to the model:
{%highlight ruby%}
class  Vote < ActiveRecord::Base
  validates_uniqueness_of :creator, scope: :voteable
end
{%endhighlight%}

###9. Using html_safe
Rails by default escapes everything so user input html, js or script tags are escaped to
prevent malicious intent.
In order to write html in any place besides the html.erb files, you must append html_safe
at the end of your statement
{%highlight ruby%}
def vote
  #post = Post.find(params[:id]) or use @post(created in the before_action)
  @vote = Vote.create(voteable: @post, creator: current_user, vote: params[:vote])
    ** We do not need to use strong_params because we are not using the key value
    pairs for mass assignment, instead we are hard coding in the keys.
  if @vote.valid?
    flash[:notice] = "Your vote was recorded"
  else
    flash[:error] = "Your vote was <strong>not</strong> recorded".html_safe
  end
    redirect_to :back #(to go back to the original page viewed)
end
{%endhighlight%}

This will cause the rails to not escape those tags and apply the html. You generally do not
want to add html_safe on user input elements.
