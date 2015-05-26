---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 2: Alternative Style of Rspec"
tags: tealeaf
---

##An Alternative Style of Rspec
- using let, before, subject, etc.

Uses lazy evaluation which means it doesn't evaluate the :todo
or the subject until it has to ...namingly when it reaches it or before.


To avoid lazy evaluation, you could do:
{%highlight ruby%}
  let!(:todo) {Todo.create(name: "cook dinner")}
{%endhighlight%}

###Pros:
is more concise and separates the common set  up code using lets

###Cons:
For a lengthy test suite it may be hard to connect the test with the description.

####Using let
Step 1. Eliminate the repetition of Todo.create in each test:
{%highlight ruby%}
describe "#display_text" do
  let(:todo) { Todo.create(name: "cook dinner") }
{%endhighlight%}

Step 2. Replace todo.display_ text
{%highlight ruby%}
describe "#display_text" do
  let(:todo) { Todo.create(name: "cook dinner") }
  let(:subject) { todo.display_text }
{%endhighlight%}

  or

Step3. Replace todo.display_text
{%highlight ruby%}
describe "#display_text" do
  let(:todo) { Todo.create(name: "cook dinner") }
  subject { todo.display_text }
{%endhighlight%}
__________________________________________
####Using before for case specific tests
{%highlight ruby%}
context "displays the name when there's no tags" do
  it "displays the name when there's no tags" do
    subject.should == "cook dinner"
  end
end

context "displays the only tag with word 'tags' when there's one tag"
  before do
    todo.tags.create(name: "home")
    todo.tags.create(name: "urgent")
  end
  it "displays name with multiple tags" do
    subject.should == "cook dinner (tags: home, urgent)"
  end
end
{%endhighlight%}

Change lengthy context to more succinct:
{%highlight ruby%}
context "displays the only tag with word 'tags' when there's one tag"
{%endhighlight%}

to =>

{%highlight ruby%}
context "multiple tags"
{%endhighlight%}

Remove the "it -text-" because its in context with the description
{%highlight ruby%}
context "one tag" do
  before { todo.tags.create(name: "home") }
  it { should == "cook dinner (tag: home)" }
end
{%endhighlight%}
