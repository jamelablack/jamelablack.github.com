---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 8: Feature Tests with Javascript"
tags: tealeaf
---
##[Selenium Webdriver](https://github.com/jnicklas/capybara#selenium)
To date, our feature tests have been fun using Capybara, which using RackTest. While it is very fast, by default - it does not support javascript. Adding ":js => true" to your test, will switch to the Capybara.javascript driver (Selenium Webdriver) - which will actually launch a browser to run the feature test.

{%highlight ruby%}

#features/visitor_makes_payment.rb
require 'spec_helper'

feature 'Visitor makes payment', js: true do

background do
  visit new_payment_path
end

scenario "valid card number" do
  pay_with_credit_card('424242424242424242')
  expect(page).to have_content('Thank you for your business!')'
end

scenairo "invalid card number" do
  pay_with_credit_card('400000000000000069')
  expect(page).to have_content('Your card has expired!')
end

scendario "declined card" do
  pay_with_credit_card('4000000000000000002')
  expect(page).to have_content('Your card was declined!')
  end
end

def pay_with_crdit_card(card_number)
  fill_in "Credit Card Number", with: card_number
  fill_in "Security Code", with: "123"
  select "3 - March", from: "date_month"
  select "2015", from: 'date_year'
  click_button "Submit Payment"
end

{%endhighlight%}

Tips:
Your Selenium Webdiver must be compatiable with your latest version of Firefox.

##[Capybara Webkit](https://github.com/jnicklas/capybara#capybara-webkit)
Since Selenium Webdriver is really slow, we will go with Capybara Webkit which is faster. This will require you to install the Capybrara Webkit gem and also install Qt locally (using homebrew). Make it the default js runner by declaring so in your spec_helper:

{%highlight ruby%}
Capybara.javascript_driver = :webkit
{%endhighlight%}

To run tests with Selenium Webdriver so you can see what is happening in the web browser, simply add "driver: selenium" to the spec:

{%highlight ruby%}
scenario "valid card number", driver: selenium do
{%endhighlight%}


##[Poltergeist](https://github.com/jnicklas/capybara#poltergeist)
Must install PhantomJs locally but it has a very nice feature set and allows for extensive customization.
