---
layout: post
title: "Rapid Prototyping with Rails: Lesson 4, part 3"
tags: tealeaf
---
##Items Covered:
1. voteable validations (ajax and regular flows)
2. exposing APIs
3. extracting common logic from models
4. creating/publishing gem

##Voteable Validation:
We want to prevent users from voting on the same post or comment twice, but at the
same time, allow them to vote on multiple posts, comments, etc.
{%highlight ruby%}
class Post < ActiveRecord::Base
  validates_uniqueness_of :creator, scope: :voteable
end
{%endhighlight%}

##Showing Js Errors when voting

You have two options:
1. Alterting out (currently implemented)
2. Traverse the DOM

Just like with using <span id> to change the number of votes, we can also create
a div.
{%highlight ruby%}
#posts/_post.html.erb
  <div id="post_vote_error_<%= post.to_param %>" class="alert alert-error" style="display: none">
    You can only vote once.
  </div>
{%endhighlight%}
{%highlight ruby%}
#posts/vote.js.erb
  if @vote.valid? %>
    ('#post_<%= @post.to_param %>_votes').html('<%= @post.total_votes %>');
  else %>
    ('#post_vote_error_<%= @post.to_param %>').show().html('<%= @vote.errors.full_messages.join'('') %>);
  end
{%endhighlight%}
______________
##APIs

APIs are how applications talk to each other. API versioning is a big deal - you have to have a versioning strategy, typically done by the date.

______

##Extracting common logic from models
{%highlight ruby%}
When you have common code in your models, you really have tow options to DRY your code:
1. place code in your superclass.
2. Extract code into a module

Extracting Common Logic from Models
-One way to do so is through module which is the next is Post.ancestors lookup chain.
1. Go into your application.rb file

  config.autoload_paths += %W(#{config.root}/lib)

So what this line of code does, is it directs your rails application to load t
his path and files in it everytime your application starts up, including the
voteable.rb file found

2. So just like with modules how we add '-able' on the end of our module name,
 we create a file called Voteable.rb under the lib folder:
 #lib/voteable.rb

  module Voteable

    class InstanceMethods

    end


    class ClassMethods

    end
  end

3. ActiveSupport::Concern
ActiveSupport is a gem.
ActiveSupport::Concern (comes with Rails 4) says that all the instance methods
listed within it are going to be instance methods when you mixin the module.

4. To include class methods within the module:

module Votable
  extend ActiveSupport::Concern

  def total_votes


  end


  module ClassMethods
    def my_class_method


    end
  end
end

5. Add the common methods the are to be extracted from your model

  module Votable
    extend ActiveSupport::Concern

      def total_votes
        self.up_votes - self.down_votes
      end

      def up_votes
        self.votes.where(vote: true).size
      end

      def down_votes
        self.votes.where(vote: false).size
      end

    end
  end

6. Add include Voteable to your model

7. Now if you run Post.ancestors => the Voteable module will be right behind the
  Post class.

8. Modules also give us an included do block:

  included do
    puts "I'm being included"
  end

  The first time the object is called, "I'm being called is printed"

9. This is a perfect candidate for has_many :votes, as: :voteable

  included do
    has_many :votes, as: :voteable
  end

*can be done for the comment and posts

{%endhighlight%}
_____________________________________
##Creating a Gem!
{%highlight ruby%}
So we were able to extract common methods into a module, but what if we needed the same
functionality across several projects:

- to list gems in terminal: gem list gem

1. You need gem: gemcutter (0.7.1)
2. mkdir voteable-gem
3. Create voteable.gemspec file within folder
4. Add the following tex t:

  Gem::Specification.new do |s|
    s.name = "votable_chris_oct"
    s.version = '0.0.0'
    s.date = '2013-10-23'
    s.summary = "A voting gem"
    s.description = "The best voting gem ever"
    s.authors = ['Jamela B.']
    s.email = 'jamela.black@gmail.com'
    s.files = ['lib/voteable_chris_oct.rb']
    s.homepage = 'http://github.com'
  end

5. Create lib folder, and voteable_chris_oct.rb file within it
6. Cut and paste common code from module

  Module VoteableChrisOct
    extend ActiveSupport::Concern

      def total_votes
        self.up_votes - self.down_votes
      end

      def up_votes
        self.votes.where(vote: true).size
      end

      def down_votes
        self.votes.where(vote: false).size
      end

    end
  end

7. Now, using gemcutter, type in terminal:

    gem build voteable.gemspec

8. Then, push to rubygems.org:

    gem push voteable_chris_oct-0.0.0.gem

9. To list your newly made gem to make sure
the push was successful:

  gem list -r voteable_chris_oct

10.  THEN ADD GEM TO YOUR GEM FILE

    gem 'voteable_chris_oct
____________________________________
You don't have to do this apparently.

10. Next you must include the file name in your application.rb file

  require 'voteable_chris_oct'

11. The include the module name in your model
   include VoteableChrisOct
_______________________________________

12. For changes or updates to your gem:
You want to change your version number in your gemspec, run gem build and push to
rubygems. Then you may want to specify the gem version in your gemfile

  gem 'voteable_chris_oct', '=0.1.0'

13. What if you don't want to publish everytime you make a change and
you just want test locally, then in your gem file you only have to specify the path up to the parent directory:

  gem 'voteable_chris_oct', path: (just run pwd in your terminal)

14. To remove a gem from rubygems, just run the following in your terminal:

  gem yank voteable_chris_oct -v '0.0.0'

15. When making changes to your gem file, you should run
bundle install --without production

16. Changes made within your module are hotlinked to your app so all changes are made live without updating your gem version
{%endhighlight%}
