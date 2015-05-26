---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 2: Alternative Style of Rspec"
tags: tealeaf
---
##Techniques you can use to grow your test suite:
Testing tags (many to many relationship with Todos)
* When testing for numbers, you want to use the 0, 1, many, and boundary_condition
(if there is one)
{%highlight ruby%}
describe "display_text" do
  it "displays the name when there's no tags"
  it "displays the only tag with word 'tag' when there's one tag"
  it "displays name with multiple tags"
  it "displays up to four tags"
  end
end
{%endhighlight%}

{%highlight ruby%}
describe "#display_text" do
  it "displays the name when there's no tags" do
   todo = Todo.create(name: "cook dinner")
   todo.display_text.should == "cook dinner"
  end

  it display the only tag with the word 'tag' when there's one tag do
    todo = Todo.create(name: "cook dinner")
    todo.tags.create(name: "home")
    todo.display_text.should == "cook dinner (tags: home)"
  end

  it "displays name with multiple tags" do
    todo.tags.create(name: "cook dinner")
    todo.tags.create(name: "home")
    todo.tags.create(name: "urgent")
    todo.display_text.should == "cook dinner(tags: home, urgent)"
  end

  it "displays up to four tags" do
    todo = Todo.create(name: "cook dinner")
    todo.tags.create(name: "home")
    todo.tags.create(name: "urgent")
    todo.tags.create(name: "help")
    todo.tags.create(name: "book")
    todo.tags.create(name: "patience")
    todo.displat_text.should == "cook dinner (tags: home, urgent, help, book, more...)"
  end
end
{%endhighlight%}

