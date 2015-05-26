---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 3: Part 1"
tags: tealeaf
---
##Items Covered:
1. Growing Complexity
2. Interactive Debugging for Solution Delivery
3. Transactions


##Growing Complexity Guided By Tests
Tags.all.map(&:name) == Tags.all.map{|tag| tag.name}

{%highlight ruby%}
def create
  @todo = Todo.new(params[:todo])
  if @todo.save
    location_string = @todo.name.split('at').last.strip
    @todo.tags.create(name: "location#(location_string)")
    redrect_to root_path
  else
  render :new
  end
end
{%endhighlight%}


strip method removes all spaces.

{%highlight ruby%}
def create
  @todo = Todo.new(params[:todo])
  if @todo.save
    location_string = @todo.name.split('at').last.strip
    locations = location_string.split('and')
    locations.each do |location|
    @todo.tags.create(name: "location:#(location)")
    redrect_to root_path
  else
  render :new
  end
end
{%endhighlight%}

###When to use each vs map
each is used for when you want to iterate through a an collection of data, an
array for example but you dont want to manipulate the result.
map is ideal for when iterating over a collection and then you need to alter of manipulate those returned values for input into another problem.

###When to use split vs regex
Use split when searching for one element
And regex when searching for more than one.

##Interactive Debugging for Solution Discovery
After writing and implementing your test cases, you should run sanity checks within your browser.
If bugs are identified, you should run write tests to isolate it the bug.

Use Binding.pry to in your controller being tested, after the problematic line.
Binding.pry is really just irb so you now have a means to experiment with your code
until you find the right solution.

For a good regex tutorial:
[Regex Tutorial](www.regular-expressions.info)
[Rubular](www.rubular.com)

.split method cannot run on nil so Ruby 1.9 introduced .try(:split),
which will run .split if not nil.


##Transactions
Wrapping batch operations into transactions:

To update the queue in MyFlix, the position entered must be an integer.
If not, the entire page will fail to update. This is unlike normal transactions
where we can include a validation error.
This is where the concept of transactions come in. If all actions in the transaction
do not save, it will rollback.

Transactions are protective blocks where SQL statements are only permanent if
they can all succeed as one atomic action. The classic example is a transfer between
two accounts where you can only have a deposit if the withdrawal succeeded and vice versa.
Transactions enforce the integrity of the database and guard the data against program errors or database break-downs.
So basically you should use transaction blocks whenever you have a number of statements that must be executed together
or not at all.
