---
layout: post
title: "Rapid Prototyping with Rails: Lesson 4, part 1"
tags: tealeaf
---
##Items Covered
1. Ajax
2. Slugs
3. Simple Admin Role
4. Time Zones
5. Exposing APIs

##Ajax
There are two forms of Javascript,
1. (UJR) Unobstursive Js - which seperates the behavior layer from the websites content
structure and presentation
2. (SJR) Server Generated Responses (The Rails Way)

Ajax is good for reloading only part of a page when all else stays the same.
Really helpful when you have an expensive page that has alot of aggregate information.
JQuery sits on top of Javascript to resolve the browser-specific qwerks
{%highlight ruby%}
An example of an Ajax request in jQuery code:
    $(document).ready(function() {
      $('#hit_form input').click(function() { ** 1. Unobstrusive javascript event listener
        $.ajax({** 2. Trigger ajax  request
        type: 'POST'
        url: 'player/hit'
        data: {param1: "hi", param2: "There"}
      }).done(function(msg) { ** 3. Handle the Response
        $('#some_element').html(msg);
      })
    });
  )};
{%endhighlight%}

So the steps to an ajax request are:
  1. Unobstrusive Javascript Event Listener
  2. Trigger ajax request
  3. Handle the response

###Step 1. Unobstrusive Javascript Event Listener
1. To add the ajax call to your up vote in your postit app, add remote: true:
<%= link_to vote_post_path(post, vote:'true'), method: 'post', remote: true do %>
  <i class='icon-arrow-down'></i>
  <% end %>
<% end %>

*This will add a data-remote="true", similar to the data-method="post"
so similarly, just as with the data-method='post' looks for data-method
and applies javascript that turns the anchor tag into 'get' link to 'post' form,
the same occurs when remote: true is identified, it turns those anchor tags
into an ajax call(as seen above)

In terminal, when clicking the up link, you should see:
Processing by PostsController#vote as JS, but normally its as html,
this let's you know that its an ajax call.


###Step 2. Trigger ajax request
We have to be able to handle this response in the PostsController#vote:
2. We have add code to handle the js request in vote controller action:
{%highlight ruby%}
  def vote
    vote = Vote.create(voteable: @post, creator: current_user, vote: params[:vote])

    respond_to do |format|
      format.html do
        if vote.valid?
          flash[:notice] = "Your vote was counted!"
        else
          flash[:error] = "Your vote was <strong>not</strong> recorded!".html_safe
        end
        redirect_to :back
      end
      format.js
    end
  end
{%endhighlight%}

We add format.js becasue instead of adding unobstrusive js, format.js will
cause rails to render a js.erb template under the same name as the controller action name.
The method determines if it can take a block. You can only render a template and redirect_to a url, the default is to render a template

###Step 3. Testing out vote.js.erb
{%highlight ruby%}
#posts/vote.js.erb
** You have access to the instance variables created in the action

alert('total votes for <%= @post.title %> is <%= @post.total_votes %>');
{%endhighlight%}

The above action creates a pop up alert but does not change the number of votes on the main page.
When inspecting the element, it shows that this is just an empty span in the html. Js needs away to
identify the empty span to change to the number, that is accomplished through an  html id class:

###Step 4. Changing the number of votes
{%highlight ruby%}
#posts/_post.html.erb

<span id='post_<%=post.id%>_votes'><%= post.total_votes %></span>
{%endhighlight%}
Rails gives you the ability to use js and parse out active record objects
which isnt possible with normal js.
So now, we can update the vote.js.erb file:
{%highlight ruby%}

$('#post_<%= @post.id %>'_votes).html ('<%= post.total_votes %>');
{%endhighlight%}
You can now also add remote: true to the down vote also.

###Step 5. Limiting the number of votes
So when add the ajax request, we now do not get an error if we try to vote
 on a post we've already voted on.
 5a) We need to turn the local variable 'vote' into an instance variable under
 the vote action:
 {%highlight ruby%}
 def vote
    @vote = Vote.create(voteable: @post, creator: current_user, vote: params[:vote])

    respond_to do |format|
      format.html do
        if vote.valid?
          flash[:notice] = "Your vote was counted!"
        else
          flash[:error] = "Your vote was <strong>not</strong> recorded!".html_safe
        end
        redirect_to :back
      end
      format.js
    end
  end
{%endhighlight%}
  5b) Write an if statement in the vote.js.erb file:
  {%highlight ruby%}
    <% if @vote.valid? %>
      $("#post_<%= @post.id %>_votes").html("<%= @post.total_votes %> votes")
    <% else %>
      alert("You can only vote on a post once.");
    <% end %>
  {%endhighlight%}


So in summary, Rails Core Developers wrote jQuery library that converts remote= true
into data-remote= true, which converts to an ajax request.
In turn, we told ajax how to respond via html and js
