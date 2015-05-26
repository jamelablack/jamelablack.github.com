---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 2: Writing Controller Tests"
tags: tealeaf
---

###With any test, there are typically 3 actions:
1. Set up the data required for the action
2. Perform the action
3. Verify the the expected result

###Controller Tests
- are typically considered more functional bc you're pulling together
multiple components

You typically never have to tests the render or redirect views portion of an action
because that is testing the functionality of rails rather than the code we write. The goal
of testing is to test the code we write only.

You do, however want to test redirect_to for unauthorized users.

Also if you dont wan't to rerun all the tests in your suite and just run one in particular
you can specify "line:27" and it will run the test on that line.

{%highlight ruby%}
rspec spec/controllers/todos_controller_spec.rb 27
{%endhighlight%}

{%highlight ruby%}
class TodoController < ApplicationController

  def index
    @todos = Todo.new
  end

  def new
    @todo = Todo.new
  end

  def create
    @todo = Todo.new(params[:todo])
    if @todo.save
      redirect_to root_path
    else
    render :new
    end
  end
end
{%endhighlight%}

###Steps:
1. Create directory: controllers
2. Create spec: todos_conroller_spec.rb
3. Controller typically have two goals... render and redirect

{%highlight ruby%}
require 'spec_helper'

describe TodoController do

  describe "GET index" do
  it "set the @todos variable" do
    cook = Todo.create(name: "cook")
    sleep = Todo.create(name: "sleep")

    get :index
    assigns(:todos).should == (cook, sleep)
  end

  it "render the index template" do
    get :index
    response.should render_template :index
    end
  end

  describe 'GET new' do
    it "sets  the @todo variable" do
      get :new
      assigns(:todo).should be_new_record
      assigns(:todo).should be_instance_of(Todo)
    end
    it "renders the new template" do
      get :new
      response.should render_template :new
    end
  end

  describe "POST create" do
    it "creates the todo record when the input is valid" do
      post :create, todo: {name: "cook", description: "I like to cook"}
      Todo.first.name.should == "cook"
      Todo.first.description.should == "I like cooking"
    end

    it "redirects to the root path when the input is valid" do
        post :create, todo: {name: "cook", description: "I like cooking"}
        response.should redirect_to root_path
    end

    it "does not create a todo when the input is invalid" do
        post :create, todo: {description: "I like cooking"}
        Todo.count.should == 0
    end

    it "renders the new template when the input is invalid" do
      post :create, todo: {description: "I like cooking"}
      response.should render_template :new
    end
end
{%endhighlight%}
