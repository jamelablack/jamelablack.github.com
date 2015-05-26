---
layout: post
title: "Rapid Prototyping with Rails: Lesson 4, Comparing Metaprogramming vs. Concerns"
tags: tealeaf
---

##There are two options for Extracting Common code:
* Metaprogramming
* Modules/Concerns

## Closer look at Metaprogramming
Metaprogamming will allow you to include a module that will allow your Instance/Class methods to run as class/instance methods:
{%highlight ruby%}
module Votetable
  def self.included(base)
    base.send(:include, InstanceMethods)
    base.extend ClassMethods
  end
{%endhighlight%}

### So for class methods:
{%highlight ruby%}
module Votetable
  def self.included(base)
    base.send(:include, InstanceMethods)
    base.extend ClassMethods
    base.class_eval do
      my_class_eval
    end
  end

  class InstanceMethods
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

  class ClassMethods
    def my_class_method
      puts "This is class method"
    end
  end
end
{%endhighlight%}

One thing to understand is that you can organize the code below into a sub-module to orchestrate how the code is called:
{%highlight ruby%}
module Votetable
  def self.included(base)
    base.send(:include, InstanceMethods)
    base.extend ClassMethods
    base.class_eval do #calling the class method each time the Post class is called
      my_class_eval
    end
  end
{%endhighlight%}

So if you went into rails console and called Post => "This is class method" would load, in addition to the Post object.


##Closer look at ActiveSupport::Concerns (Modules)
 We can also extract our has_many :votes, as: :voteble association, but we can place this in a class method, which will allow it to be fired automatically when the Post class is called
 just as though its in the model:
 {%highlight ruby%}
   class ClassMethods
     has_many :votes, as: :voteable
   end
{%endhighlight%}
###Using Concerns
{%highlight ruby%}
   module Voteable
     extend ActiveSupport::Concern

     included do
       has_many :votes, as: :voteable
     end


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
{%endhighlight%}

###Using Metaprogramming
{%highlight ruby%}
   module Voteable
     def self.included(base)
       base.send(:include, InstanceMethods)
       base.extend ClassMethods
     end

     class InstanceMethods

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

     class ClassMethods
       has_many :votes, as: :voteable
     end
   end
{%endhighlight%}
