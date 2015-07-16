---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 8: Transactions & Test Database Setup"
tags: tealeaf
---
##Transactions and test database setup

If you look at the instructions for capybara here: https://github.com/jnicklas/capybara

In the "Transactions and database setup" section, it talks about the Selenium driver needs to run against a real HTTP server and that capybara will start one for you but it runs on a different thread. In this case using "transactions" as the strategy to reset the database after each spec can be a problem, and this is where you want to use the "database_cleaner" gem so that you can use the "truncation" strategy, which effectively just goes to your database and delete all the records. It's a bit slower than transaction rollbacks, but in this case guarantees data will be reset.

You can add the "database_cleaner" gem in your test group, and use the following configuration in your spec_helper file inside of the RSpec.configure do |config| ... end block.



{%highlight ruby%}
config.before(:suite) do
  DatabaseCleaner.clean_with(:truncation)
end

config.before(:each) do
  DatabaseCleaner.strategy = :truncation
end

config.before(:each, :js => true) do
  DatabaseCleaner.strategy = :truncation
end

config.before(:each) do
  DatabaseCleaner.start
end

config.after(:each) do
  DatabaseCleaner.clean
end
{%endhighlight%}
