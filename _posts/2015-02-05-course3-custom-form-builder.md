---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 1: Custom Form Builders"
tags: tealeaf
---
Let's say we have a Todo's app, where we allow our users to create a new todo. We also want to show errors when validation errors occur:
{%highlight ruby%}
#new.html.haml
%section.new_todo
  %h3 Add a new todo
  = form_for @todo do |f|
    = f.label :name, "Name"
    = f.text_field :name
    = f.label :description, rows: 6
    %br
    = f.submit "Add This Todo"
{%endhighlight%}

The issue with this code above is that it is not pulling in the
errors on the Todos object.

We could:
{%highlight ruby%}
#new.html.haml
%section.new_todo
  %h3 Add a new todo
  = form_for @todo do |f|
  if@todos.errors?
    = f.label :name, "Name"
    = f.text_field :name
{%endhighlight%}

...but this is cumbersome to do for each form.


Lets create a helper called MyFormBuilder that will customize the label:

{%highlight ruby%}
class MyFormBuilder < ActionView::Helpers::FormBuilder
  def label(method, text = nil, options = {}, &block)
    errors = object.errors[method.to_sym]
    if errors
      text += " <span class =\"error">#{errors.first}</span>" #pull the first error from the errors object (an array)
    end
    super(method, text.html_safe, options, &block) #delegates control back to the custom form builder
    end
  end
{%endhighlight%}

Update new.html.haml, make sure you add "MyFormBuilder" to your form_for block:
{%highlight ruby%}
%section.new_todo
  %h3 Add a new todo
  = form_for @todo, builder: MyFormBuilder do |f|
    = f.label :name, "Name"
    = f.text_field :name
    = f.label :description, rows: 6
    %br
    = f.submit "Add This Todo"
{%endhighlight%}
_____
###Alternate Option: my_ form_ for helper

This is still somewhat repetitive. Let's create a my_ form_ for helper. We can manually merge in the my_form_for rather passing in the MyFormBuilder:

{%highlight ruby%}
app/helpers/my_form_builder.rb
module ApplicationHelper
  def my_form_for(record, options = {}, &proc) #takes 3 arguements
    form_for(record, options.merge!({builder: MyFormBuidler}), &proc)
  end
end
{%endhighlight%}

####Update the new.html.haml
{%highlight ruby%}
%section.new_todo
  %h3 Add a new todo
  = my_form_for @todo do |f|
    = f.label :name, "Name"
    = f.text_field :name
    = f.label :description, rows: 6
    %br
    = f.submit "Add This Todo"
{%endhighlight%}

___________________________
##Custom Form Builders in The Wild!
###1. Formtastic
* You can use semantic form_ for

###2. Simple_form (creators of devise)
* allows integration with Twitter Bootstrap & Zurb Foundation 3
* is not best for those with alot of customization
* has default mapping - which is not the best for complex forms

###3. BootstapForm
* minimal dsl
* similar flow/syntax to Rails
