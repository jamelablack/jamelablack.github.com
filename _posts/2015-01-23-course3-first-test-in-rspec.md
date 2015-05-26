---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 1: First test in Rspec"
tags: tealeaf
---


1. Add Rspec to your gemfile, make sure to include in both test and development:
{%highlight ruby%}
  group :test, :development do
    gem 'rspec-rails'
  end
{%endhighlight%}
2. rails g rspec:install

Generates three items:
  1. .rspec file - contains "color".. just means you want your output to be color-coded
  2. spec folder
  3. spec helper - where configuration goes

3. Create models folder inside spec folder

4. Create a todo_spec.rb within the models folder (name of model)

5. Within todo_spec.rb you must:
{%highlight ruby%}
  require 'spec_helper'

  describe Todo do
    it "saves itself" do
      todo = Todo.new(name: "cook dinner", description: "love me some din din")
      todo.save
      Todo.first.name.should == "cook dinner"
    end
  end
{%endhighlight%}

  ** "it" is a keyword that begins each test

  ** "do" implements the test

  ** .should is another keyword will test to see if we have the desired result.

  ** == is the matcher

##How to run rspec outside of Rails
1. Create a project folder
2. Inside the project folder, create a lib folder. Your code that you're testing will go inside here.
3. Create another folder: spec, where your .spec test files will go.
4. Make sure to require your code files inside your specs.
