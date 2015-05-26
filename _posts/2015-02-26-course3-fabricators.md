---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 2: Object Generation with Fabricators"
tags: tealeaf
---
It's typical to create many objects during testing and the more attributes you have
the more cumbersome it could be to maintain, moreover, if an attribute changes, it then becomes very difficult
to update all those tests.


To make this better, we can use an object generator framework called Fabrication.

###Steps:
####1. Create a folder under specs called "Fabricators"
####2. create fabricator_todo.rb

{%highlight ruby%}
  Fabricator(:todo) do
    name { "cooking" }
  end
{%endhighlight%}

####3. You can also create a user each time a todo is created:
{%highlight ruby%}
Fabricator(:todo) do
  name { "cooking" }
  user { Fabricate(:user) }
end
{%endhighlight%}
  or

{%highlight ruby%}
Fabricator(:todo) do
  name { "cooking" }
  user
end
{%endhighlight%}

###In your test suite:
If you want to create an object in your database:

{%highlight ruby%}
describe "#display_text" do
  let(:todo) { fabricate(:todo) }
  subject { todo.display_text }
{%endhighlight%}


If you would rather create an object in memory, you could:

{%highlight ruby%}
describe "#display_text" do
  let(:todo) { fabricate.build(:todo) }
  subject { todo.display_text }
{%endhighlight%}
