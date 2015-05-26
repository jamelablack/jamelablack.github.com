---
layout: post
title: "Rapid Prototyping with Rails: Lesson 2, part 3"
tags: tealeaf
---
#Items covered:
1. Nested Resources
2. Helpers
3. Fat Models, Thin Controllers
4. Drop-downs
5. Application Helpers

Review nested resource
Setting up categories association in post form

1. How to create a comment in the show posts page.

1.1 We need to know how the route to submit to:
* rake routes | grep comments (console)

or

* localhost:3000/rails/info/routes (browser)

1.2 How do we tell our model backed form to submit to the POST create comments action?

By adding @comments to our model backed form_for POST create posts:

{%highlight ruby%}
  show.html.erb
  <h5>Create a Comment </h5>
  <%= form_for [@post, @comment] do |f| #[parent obj, child obj] to create this 'posts/id/comments'
    <f.text_area :body %>
      <br/>
    <%= f.submit "Create Comment", class: 'btn btn-primary' %>
  <% end %>
{%endhighlight%}

If ever you feel as though you can't tell which object you are passing through the form_for:
look at the create controller action - there it will tell you where you one which object you are calling
.save on. This is the object that will generate the error if it does not pass validations.

Helpers: What if we wanted to link_to post.url in the show page?
{%highlight ruby%}
show.html.erb

<h4> Hi there, you're viewing <%= link_to @post.title, @post.url.starts_with?('http://') ?
@post.url : "http://#{post.url}"" %></h4>
{%endhighlight%}

##Helpers:
We don't want our model to be concerned with presentation level concerns.
{%highlight ruby%}
  application_helper.rb
    module ApplicationHelper
      def fix_url
        str.starts_with?('http://') ? str : "http://#{str}"
      end
    end
{%endhighlight%}

{%highlight ruby%}
  show.html.erb
  <h4>Hi there, you're viewing <%= link_to post.title, fix_url(post.url) %></h4>
{%endhighlight%}


##Fat Models, Thin Controllers
Move logic to a model method
Problems with logic at the controller level:
1. Sometimes you can't tell where the input stream is (console, another action)
2. Its really hard to test

**How to associate a post and category in a form?
In order to use the form_for, you must use a getter or setter (colmun name of virtual attribute

In console:
{%highlight ruby%}
  1. post = Post.first
  2. post.categories.first.name => "Rubyists"
  3. post.categories = [] => Delete category associations
  4. post.category_ids = [2, 3] => add category_ids 2 and 3 to post, saves to database
  5. post.categories.size => 2
  6. post.category_ids = [4] # removes categories previously entered and saves caegory_id 4
{%endhighlight%}


##How to create a drop down
Learn how to do use Select forms in HTML first:
{%highlight ruby%}
*Selecting one option:
<select>
  <option value='1'>option 1</option>
  <option value='2'>option 2</option>
</select>
** Selecting multiple options
<select name='post[category_ids][]' multiple ='multiple'>
  <option value='1'>Rubyists</option>
  <option value='2'>Pythonistas</option>
</select>
{%endhighlight%}

But helpers will save in memory your previous selections rather than hard coding
when edit/updating a post.

Note: that name='post[category_ids]' requires an [] for arrays.

###Strong_params for checkboxes or drop-downs
{%highlight ruby%}
-> category_ids requires an array
    params.require(:post).permit(:title, :url, :description, category_ids:[])
{%endhighlight%}

##Application_helper
Is were you store redundant, reusable code that shouldn't be stored at the model layer.
The model should never deal presentation level concerns but rather pure logic, data structures (arrays and hashes).
It should present data in whatever form and the views and helpers should just deal with it
accordingly.
Helpers are good for extracting logic that deals with the view but doesn't belong to a particular model
or resource.
