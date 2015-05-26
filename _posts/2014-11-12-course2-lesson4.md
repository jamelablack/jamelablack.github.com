---
layout: post
title: "Rapid Prototyping with Rails: Lesson 4, part 2"
tags: tealeaf
---
##Items covered:
1. Slugging
2. Single Admin Role
3. Timezones
4. Users select their own timezone

##Slugging
a custom URL generated based off of some characteristic of the page being viewed.

###In our case, we care about from a slugging perspective:
1. Posts
2. Categories
3. Users

###What are the benefits of Slugging:
1. SEO friendly/user ease
2. Security (exposing primary key ids)
3. Prevent those from knowing how many users you have

To set up a slug:
{%highlight ruby%}
#posts/_post.html.erb

<span>
  <%= link_to('#{post.comments.size} comments', post_path(post))
</span>

* the post_path(post) is actually calling the to_params method on the post object:

<span>
<%= link_to ('#{post.comments.size} comments', post_path(post.to_params)) %>
</span>
{%endhighlight%}

###1. So how can we change the to_params method?
Within our model, we can declare an instance method where the to_params method
will go to our slug instead of the params id.
{%highlight ruby%}
def to_param
  self.slug
end
{%endhighlight%}

###2. We must also create a migration to add our slug column to our existing table:
{%highlight ruby%}
rails g migration add_slug_to_posts
{%endhighlight%}

###3. Open Migration File
{%highlight ruby%}
  change :posts |t| do
    add_column :posts, :slug, :string
  end
{%endhighlight%}
###4. In Model, we must create a method to generate the slug.

Take time to create a method using gsub/regex (for edge cases)

{%highlight ruby%}
test out regex on rubylur.com
{%endhighlight%}

One example:
{%highlight ruby%}
def generate_slug
  self.slug = self.title.gsub(' ', '-').downcase
  self.save # we hate calling .save explicitly, maybe we can add it a callback.
end
{%endhighlight%}

###5. ActiveRecord Callbacks (look up online, listed in order of workflow)
Methods the are exposed to use as a apart of the lifecycle of an ActiveRecord object,
so we can insert or make changes to any of the callbacks.

A couple things to consider - do we want to generate the slug after created only,
or after the post is created and updated?

We call insert add generate_slug method after @post.save:
{%highlight ruby%}
after_save :generate_slug
{%endhighlight%}

But we dont want to create slugs off of bad urls!
{%highlight ruby%}
So how about:
    after_validations :generate_slug
    or
    before_save :generate_slug
{%endhighlight%}
* This code goes in the model
* Know the difference between
{%highlight ruby%}
before_save  - update slug whenever title changes
before_create
{%endhighlight%}

###6. So if I go with before_save and then all my existing posts will blow up.
Why? because they do not have slugs. So I must go into to the Rails console and run
Posts.all.each {|post| post.save}
This is will trigger the before_save action and create slugs for all existing posts before saving.

The above console command is not good for when in production, better to run a migration.

###7. Now when visiting the link:
{%highlight ruby%}
<span>
  link_to('#{post.comments.size} comments', post_path(post))
</span>
{%endhighlight%}
When using named routes, always use objects instead of hard-coding,
bc on objects you can call to_params and its useful in case you want to sluggify.

Now we must update the set_post method in our posts_controller since
we've changed the to_param method:
{%highlight ruby%}
OLD:
  def set_post
    @post = Post.find(params[:id])
  end
{%endhighlight%}

{%highlight ruby%}
NEW:

def set_post
  @post = Post.find_by(slug: params[:id])
end
{%endhighlight%}

###8. Same thing goes for our comment which uses @post
{%highlight ruby%}
#comments_controller

  def create
    @post = Post.find_by(slug: params[:post_id])
  end
{%endhighlight%}

* One of the best slugging gems is friendly_id


###9. Update the span id
{%highlight ruby%}
<span id='post_<%=post.id%>_votes>' to be

<span id='post_<%=post.slug'%>_votes'> in your _post.html.erb partial and your
  vote.js.erb
{%endhighlight%}

##Single Admin Role
Define a number of roles and each roles has a set of permissions
User has many roles, A Role has many permissions.

You must create has_many through association. Over all, having various roles with permissions is
discouraged because every action is required to be checked against a set of permissions and this will
not only greatly complicate development and slow the application down.

For simple apps, a single admin role is typically best
This requires having a role column on users that takes a string as free form text, which allows you to
specify whatever role you want. This doesnt allow for the most flexiiblity but will give you keep you
from having to create a roles table and permissions table.

###1. Create migration to add roles to users table:
{%highlight ruby%}
rails g migration add_roles_to_users
{%endhighlight%}
###2. Open migration file, add syntax

{%highlight ruby%}
  def change
    add_column :users, :role, :string

  end
{%endhighlight%}
###3. Create roles in user model
{%highlight ruby%}
  def admin?
    self.admin == 'admin'
  end

  def moderator?
    self.role == 'moderator?'
  end
{%endhighlight%}

  * Or you can use a symbol for performance optimization
{%highlight ruby%}
  def admin?
    self.role.to_sym == :admin
  end

  def moderator?
    self.role.to_sym == :moderator
  end
{%endhighlight%}

###4. So what if we said "In order to create a category, you must be an admin"

We could create a before_action :require_admin in the categories_controller
{%highlight ruby%}
  class CategoriesController < ApplicationController
    before_action :require_user
    before_action :require_admin
  end
{%endhighlight%}

###5. We want to create the require_admin method in the application_controller as
we did with require_user. We want to make sure that the user is logged_in? .
If the current_user is an an admin but not logged_in, an exception will be thrown
because you must consider nil condition.
{%highlight ruby%}
def require_admin
  access_denied unless logged_in? and current_user.admin?
end

def access_denied
  flash[:error] = "Sawry.. you can't do that."
  redirect_to root_path
end
{%endhighlight%}

###6. Move New Category Link (and New Post Link) under if logged_in? under _navigation.html_erb
{%highlight ruby%}
#layout/_navigation.html.erb

<% if logged_in? and current_user.admin?
  <li>
    <%= link_to "New Post", new_post_path %>
  </li>
  <li>
    <%= link_to "New Category", new_category_path %>
  </li>
  <% end %>
<% end %>
{%endhighlight%}
___________________________________
##Timezones
###1. We have an existing helper_method in our application_helper.rb that displays timezone,
we can just add %Z to include timezone:
{%highlight ruby%}
  def display_datetime(dt)
    dt.strftime("%m%d%Y %1:%M%P %Z")
  end
{%endhighlight%}

###2. In your application.rb file, uncomment out:
{%highlight ruby%}

  config-time_sone = 'Central Time (US & Canada)'
{%endhighlight%}
###3. Now we need to find the string for setting the default time to Eastern Standard Time
{%highlight ruby%}
  - run rake -T | grep time => rake time:zones:all
  - run rake time:zones:all => displays all timezones available for rails
  - run rake time:zones:all | grep US => 'Eastern Time (US & Canada)'
{%endhighlight%}
###4. Anytime you make changes to the application.rb file, you must restart the server
_____________
##Users select their own timezone
This is great, but what if we want users to select their own timezone:
###1. Create Rails migration add timezone column to users
rails g migration add_timezones_to_users

###2. class AddTimeZonesToUsers < ActiveRecord::Migration
{%highlight ruby%}
  def change
      add_column :users, :time_zones, :string
    end
  end
{%endhighlight%}
###3. We want to add the ability for users to select their timezone upon registration:
{%highlight ruby%}
<div class='control-group'>
  <%= f.label :time_zone %>
  <%= f.time_zone_select :time_zone, ActiveSupport::TimeZone.us_zones %>
</div>
{%endhighlight%}
This time_zone_select comes with Rails 4 only and newer. The ActiveSupport::TimeZone.us.zones will display all US timezones at the top. Reference documentation for more option

###4. Check what data can be submitted by adding a binding.pry to the user#create
action and running params in rails console

###5. user_params will show what params have been submitted, and we can see that timezone was not saved because we need to add it to strong_params
{%highlight ruby%}
    def user_params
      params.require(:user).permit(:username, :password, :time_zone)
    end
{%endhighlight%}
###6. Once logged in, we want the users timezone to be displayed, we must edit the display_datetime method
{%highlight ruby%}
in the ApplicationHelper:

  module ApplicationHelper
    def display_datetime(dt)
      if logged_in? && !current_user.time_zone.blank?
        dt = dt.in_time_zone(current_user.time_zone)
      end
    end
  end

Time_zone method will display the object's time if its passed a string. So in rails console,if you run post.created_at.in_time_zone("Arizona") => will return the datetime object
{%endhighlight%}

To understand more about the time_zone_select or the in_time_zone method, look up the documentation.

###7. If we wanted to specify the default time in our drop down
{%highlight ruby%}
      <div class='control-group'>
    <%= f.label :time_zone %>
    <%= f.time_zone_select :time_zone, ActiveSupport::TimeZone.us_zones, default: Time.zone.name%>
  </div>
{%endhighlight%}
