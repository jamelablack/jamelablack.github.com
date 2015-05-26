---
layout: post
title: "Rapid Prototyping with Rails: Lesson 3, part 3"
tags: tealeaf
---
##Items Covered
1. polymorphic associations
  * databases (syntax)
  * models (syntax)
2. Voting


##Polymorphic Associations:

At the database level:

 When you have one subject but many objects for example, 1 comment or like can be placed on
 many objects or foreign keys - post_id, photo_id, video_id, etc.
 - This creates alot of spaces and ineffiencies in the database.
 This is solved by restricting the database from:
 {%highlight ruby%}

 "id, body, user_id, post_id, photo_id, video_id" to

 "id, body, user_id, commentable type, commentable_id"
 {%endhighlight%}

{%highlight ruby%}
 commentable_type: must be the model name, capitialize first initial, string
 commentable_id: is the primary_key on the 1 side.

 commentable_type to commentable_id: is a composite foreign_key
{%endhighlight%}

 In our application, we will implement voting using polymorphic associations and we can
 practically vote on anything, but in this case, we will vote on just posts and comments.


###Step 1: Generate Migration
 {%highlight ruby%}
 Create votes table
  =>rails g migration create_votes
{%endhighlight%}

###Step 2: Generate Table
 {%highlight ruby%}
create_table :votes do |t|
  t.boolean :vote
  t.integer :user_id
  t.string :voteable_type
  t.integer :voteable_id
  t.timestamps
  end
end
{%endhighlight%}
  or use
{%highlight ruby%}
t.references :voteable, polymorphic: true
{%endhighlight%}

###Step 3: Create Vote Model
{%highlight ruby%}
class Vote < ActiveRecord::Base
  belongs_to :creator, class_name: 'User', foreign_key: 'user_id'
  belongs_to :voteable, polymorphic: true
end
{%endhighlight%}

{%highlight ruby%}
class User < ActiveRecord::Base
  has_many :votes
end
{%endhighlight%}

{%highlight ruby%}
class Post < ActiveRecord::Base
  has_many :votes, as: voteable
end
{%endhighlight%}

Do the same for comments.

The above action gives you voteable getters/setters and now you can assign a variable to your user and call .votes

{%highlight ruby%}
So in the console:
    v = Votes.first
    v.voteable => nil
    post = Post.first
    post.votes << v
    or another way to assign is
    v.votable = Comments.first
    v.save
{%endhighlight%}

In the above - the user and vote are the subjects while the comments and posts
are the objects being voted on.

###Remember
* Subjects - has_many, can use same gatters and setters as ususal
* Objects - belongs_to, will expect subject foreign_keys, voteable is now your getter/setter
  where you pass voteable an object or set the voteable

##Voting

Step 1. Now let's show votes on posts index page
{%highlight ruby%}
#posts/index.html.erb
<div class='row'
  <div class='span0 well text-center'>
  <%= link_to '' do %>
    <i class='icon-arrow-up'></i>
    <% end %>
    <br/>
  <%= link_to '' do %>
    <i class='icon-arrow-down'></i>
    <% end %>
</div>
{%endhighlight%}

Step 2. How do we reflect the increase/decrease of votes every time the link is clicked:
{%highlight ruby%}
#routes.rb
Two ways....
a) POST/votes => 'VotesController#create'
  - needs to pass in two pieces of information (1 - whether its the post/comment
  being voted on, 2. The post/comment id
  - also this would create another top level resource)
  - best for if you're voting on alot of objects

        How to implement this in your routes:
        resources :votes, only: [:create]

b) POST/posts/3/vote => 'PostsController#vote'
POST/posts/3/comments/4/vote => 'CommentsController#vote'
    How to implement this in your routes, you must use something called a member, where
    each action will be exposed to the member of that url

    resources :posts, except: [:destroy] do

      member do
        post :vote
      end

      (where post is the member and vote is the action)


      **rake routes | grep vote
      vote_post POST /posts/:id/vote(.:format)   post#vote
{%endhighlight%}

Step 3. Also what about if we want to see archives of our posts
{%highlight ruby%}
  # GET /posts/archives

  collection do
    get :archives
  end
  **rake routes | grep archives
  archives_posts GET /posts/archives(.:format)  posts#archives
{%endhighlight%}

So to compare using post vs get, post will require pass in an object.
So basically you can create any route that you want using member, collections, and nested resources

##Steps from UX
* Lofi/hifi
* ERD
* Tables
* URL design


Step 4. Let's include these new routes in our posts/index.html.erb
{%highlight ruby%}
#posts/index.html.erb
<div class='row'
  <div class='span0 well text-center'>
  <%= link_to vote_post_path(post) method: 'post' do %>
    <i class='icon-arrow-up'></i>
    <% end %>
    <br/>
  <%= link_to '' do %>
    <i class='icon-arrow-down'></i>
  <% end %>
</div>
{%endhighlight%}


You must specify method: 'post' because with out it the default will be the GET method and the link, post/id/vote will not work on a GET.
* method: 'post' create data method: There is javascript that comes with rails
that looks for anchor tags and the data-method="post",  that will turn the link
into an actual form and submit content.
