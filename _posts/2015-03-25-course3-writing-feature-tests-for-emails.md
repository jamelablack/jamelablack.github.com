---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 6: Writing Feature Tests for Emails"
tags: tealeaf
---
##Interact with Emails in Feature Tests:

Sometimes applications may require you to complete a form and an email is
is generated.
To test this feature, Capybara-email is a great way to test this functionality and provides a nice interface that allows you to write specs on the level of user interaction. This is better than as creating a feature test for ActionMailer and its deliveries because it will require too much low level detail.

{%highlight ruby%}
 gem 'capybara-email'
 documentation: github//dockyard/capybara-email
{%endhighlight%}

 Example spec for inviting a user:

{%highlight ruby%}
#suser_resets_password_spec.rb
require 'spec_helper'
feature 'User resets password' do
  scenario 'user successfully resets the password' do
    alice = Fabricate(:user, password: 'old_password')
    visit sign_in_path
    click_link "Forgot Password"
    fill_in "Email Address", with: alice.email
    click_button "Send Email"

    open_email(alice.email)
    current_email.click_link("Reset My Password")

    fill_in "New Password", with: "new_password"
    click_button "Reset Password"

    fill_in "Email Address", with: alice.email
    fill_in "Password", with: "new_password"
    click_button "Sign in"
    expect(page). to have_content("Welcome, #{alice.full_name}")

    clear_email
  end
end

{%endhighlight%}
