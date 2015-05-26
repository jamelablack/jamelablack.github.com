---
layout: post
title: "Rapid Prototyping with Rails: Lesson 4, ActiveSupport::Concerns"
tags: tealeaf
---
##DRYing up code with ActiveSupport::Concerns

{%highlight ruby%}
1. Extract common code from Post/Comment Model abd place into Sluggable module
module Slugglable
  extend ActiveSupport::Concern

  included do
    before_save :generate_slug!
  end

  def to_param
    self.slug
  end

  def generate_slug!
    the_slug = to_slug(self.title)
    post = Post.find_by slug: the_slug
    count = 2
    while post && post != self
      the_slug = append_suffix(the_slug, count)
      post = Post.find_by slug: the_slug
      count += 1
    end
    self.slug = the_slug.downcase
  end

  def append_suffix(str, count)
    if str.split('-').last.to_i != 0
      return str.split('-').slice(0...-1).join('-') + "-" + count.to_s
    else
      return str + "-" + count.to_s
    end
  end

  def to_slug(title)
    str = title.strip
    str.gsub! /\s*[^A-Za-z0-9]\s*/, '-'
    str.gsub! /-+/, "-"
    str.downcase
  end
end


2. One major problem with this is that the module has to work for both comments and posts
yet the model and column names are currently hardcoded.
The first fix is changing the class to thed class to self.class:

    def generate_slug!
    the_slug = to_slug(self.title)
    post = self.class.find_by slug: the_slug
    count = 2
    while post && post != self
      the_slug = append_suffix(the_slug, count)
      post = self.class.find_by slug: the_slug
      count += 1
    end

3. Now to change the column names, you have to create a class method that we can call to
 pass the column name:

 module Slugglable
   extend ActiveSupport::Concern

   included do
     before_save :generate_slug!
     class_attribute: :slug_column
   end

   def to_param
     self.slug
   end

   def generate_slug!
     the_slug = to_slug(self.send(self.class.slug_column.to_sym))
     post = self.class.find_by slug: the_slug
     count = 2
     while post && post != self
       the_slug = append_suffix(the_slug, count)
       post = self.class.find_by slug: the_slug
       count += 1
     end
     self.slug = the_slug.downcase
   end

   def append_suffix(str, count)
     if str.split('-').last.to_i != 0
       return str.split('-').slice(0...-1).join('-') + "-" + count.to_s
     else
       return str + "-" + count.to_s
     end
   end

   def to_slug(title)
     str = title.strip
     str.gsub! /\s*[^A-Za-z0-9]\s*/, '-'
     str.gsub! /-+/, "-"
     str.downcase
   end


   module ClassMethods
     def sluggable_column(col_name)
       self.slug_column = col_name
     end
   end

 end

 5. Replace the local variable, post
 def generate_slug!
   the_slug = to_slug(self.send(self.class.slug_column.to_sym))
   obj = self.class.find_by slug: the_slug
   count = 2
   while obj && obj != self
     the_slug = append_suffix(the_slug, count)
     obj = self.class.find_by slug: the_slug
     count += 1
   end
   self.slug = the_slug.downcase
 end
 6. Define sluggable_column in Post, User, and Category Models

    sluggable_column :title

{%endhighlight%}

##Rails Console Commands:
{%highlight ruby%}
 cat ~/.gem/credentials
{%endhighlight%}
* Adds your rubygems api so you have to continuouasly log in.

{%highlight ruby%}
 gem list -r voteable_jam
 {%endhighlight%}

* Lists gems that are remote

