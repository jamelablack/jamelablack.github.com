---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 3: Part 3"
tags: tealeaf
---
##Items Covered:
1. Feature Specs
2. Capybara
3. Request Specs

###Types of Testing We've Covered:
* Models (Model Specs)
* Controllers (Controller Specs)
* Views, Routes, Helpers, Mailers (Feature Specs)

##Feature Specs
Feature Specs cover all the above in integration, although there are also specs
for every item listed above, however mailers, routes, views, and helpers do not need to be tested in isolation.

With feature specs, you mimic the user's experience originating with the browser.
This is the first example of vertical integration.
Specs are typically broken into features.

There is something called "request specs" which focuses more horizontal integration. This is when you want to test multiple requests and responses across various controllers, making sure that things flow in a sequence.

In this course, we will NOT use request_specs, becasue typically request_specs are used to capture
business processes from end to end. However any business process worth measuring must be
centered around the customer expereince...


##Capybara

The Capybara gem is the way you can simulate user interaction with the browser.
Load RSpec 2.x support by adding the following line (typically to your spec_helper.rb file):

{%highlight ruby%}
require 'capybara/rspec'
{%endhighlight%}

If you are using Rails, put your Capybara specs in spec/features.

If you are not using Rails, tag all the example groups in which you want to use Capybara with :type => :feature.

You can now write your specs like so:

{%highlight ruby%}
describe "the signin process", :type => :feature do
  before :each do
    User.make(:email => 'user@example.com', :password => 'password')
  end

  it "signs me in" do
    visit '/sessions/new'
    within("#session") do
      fill_in 'Email', :with => 'user@example.com'
      fill_in 'Password', :with => 'password'
    end
    click_button 'Sign in'
    expect(page).to have_content 'Success'
  end
end
{%endhighlight%}

Use :js => true to switch to the Capybara.javascript_driver (:selenium by default), or provide a :driver option to switch to one specific driver. For example:

{%highlight ruby%}
describe 'some stuff which requires js', :js => true do
  it 'will use the default js driver'
  it 'will switch to one specific driver', :driver => :webkit
end
{%endhighlight%}

**Capybara Built in DSL (reads more like UAT - User Accpetance tests) - reads more higher level
- feature is in fact just an alias for describe ...,
- :type => :feature,
- background is an alias for before, scenario for it,
- and given/given! aliases for let/let!, respectively.

Finally, Capybara also comes with a built in DSL for creating descriptive acceptance tests:

{%highlight ruby%}
feature "Signing in" do
  background do
    User.make(:email => 'user@example.com', :password => 'caplin')
  end

  scenario "Signing in with correct credentials" do
    visit '/sessions/new'
    within("#session") do
      fill_in 'Email', :with => 'user@example.com'
      fill_in 'Password', :with => 'caplin'
    end
    click_button 'Sign in'
    expect(page).to have_content 'Success'
  end

  given(:other_user) { User.make(:email => 'other@example.com', :password => 'rous') }

  scenario "Signing in as another user" do
    visit '/sessions/new'
    within("#session") do
      fill_in 'Email', :with => other_user.email
      fill_in 'Password', :with => other_user.password
    end
    click_button 'Sign in'
    expect(page).to have_content 'Invalid email or password'
  end
end

{%endhighlight%}

###Capybara Drivers
1. RackTest is really fast, and does not really fire-up the browser. It's a headless driver
therefore doesn't really simulate the browser experience. RackTest does not support Js.

2. Selenuim is a popular driver that does handle Js and fire-ups Firefox.
But this option is very slow.

3. Capybara Webkit is another headless driver that can execute Js well.
It's much faster than selenium but slower than RackTest.

###First feature spec

{%highlight ruby%}
require 'spec/helper'
feature 'User signs in' do
  background do
    User.create(username: "john", full_name: "John Doe")
  end

  scenario "with existing username" do
    visit root_path
    fill_in "Username", with: "john"
    click_button "Sign in"
    page.should have_content "John Doe"
    end
  end
{%endhighlight%}

  The 'fill_ in "Username", ' is referring to the label_tag :username, "Username"
  - you can also refer to fill_ in the label tag, the name, or the input_ id,
  although its best practice to use the label tag because it easier to read.

###Request Specs
Watch RailsCast video on [Capybara & Request Specs](http://railscasts.com/episodes/257-request-specs-and-capybara)

* Request Specs are alternatives to the Rails Builtin Integration Testing
  (Refer to RailsCast video 187)

In terminal, run:

{%highlight ruby%}
1. rails g rspec:install

2. rails g intergration_test task

3. Open up task_spec, code should be fine as is, but you would
change the request verb and the assertion

4. rake spec:requests
{%endhighlight%}

Example:

{%highlight ruby%}
describe "Tasks" do
  describe "GET /tasks" do
    it  "displays tasks" do
      Task.create!(:name => "paint fence")
      get tasks_path
      response.body.should include("paint fence")
      end
    end
  end

describe "POST /tasks" do
    it "create task" do
      post_via_redirect tasks_path, tasks: {name: "mow lawn"}
      response.body.should include("mow lawn")
    end
  end
end
{%endhighlight%}

One issue with request specs this that they dont mimic the user's experience
becasue you'e submitting the request directly, rather than going through the form
like the user would - that is why we use Capybara (rather than webrat) & Launchy

Installing Capybara, allows us to change our existing tests:

{%highlight ruby%}
describe "Tasks" do
  describe "GET /tasks" do
    it  "displays tasks" do
      Task.create!(:name => "paint fence")
      visit tasks_path
      page.should include("paint fence")
      end
    end
  end

describe "POST /tasks" do
    it "create task" do
      visit tasks_path
      fill_in "New Task", with: "mow lawn"
      click_button "Add"
      page.should have_content("successfully added task")
      page.should have_content("mow lawn")
    end
  end
end
{%endhighlight%}

*What if the test fails and you don't know why?
Thats where the gem "Launchy" comes in:
 -anywhere in the test, you can write:

{%highlight ruby%}
  save_and_open_page
{%endhighlight%}

Capybara does not test Javascript with out Selenium:

{%highlight ruby%}
 it "supports js", js: true do
   visit tasks_path
   click_link "test js"
   page.should have_content("js works")
  end
end
{%endhighlight%}
___
Let's try adding Js to our first test and see if it is supported by
Selenium.

{%highlight ruby%}
describe "Tasks" do
describe "GET /tasks" do, js: true do
  it  "displays tasks" do
    Task.create!(:name => "paint fence")
    visit tasks_path
    page.should include("paint fence")
    end
  end
end
{%endhighlight%}

The test will fail because Selenium does not support database transactions

###In #spec_helper.rb:
{%highlight ruby%}
Change config.use_transactional_ fixtures = true, to false.
{%endhighlight%}
Selenium does not support database transactions.

But this will mean our database transactions will carryover in between
tests and we dont want that. So the answer is the database cleaner
gem that will run in between tests. You must add the config code
from the documentation into the spec_helper, removing transactions since the
arent supported by Selenuim.

_______

To run pry in a spec, you must use:
require 'pry'; binding.pry before the error/after the action
During pry. you can use
{%highlight ruby%}
Launchy "save_and_open_page"
{%endhighlight%}
