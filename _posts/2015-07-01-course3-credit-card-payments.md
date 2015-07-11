---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 7: Processing Credit Card Payments Online with Stripe"
tags: tealeaf
---
##Processing Credit Card Payments Online

Back in the day, processing credit cards online was intense as told by 37Signals in their [blogpost](https://signalvnoise.com/posts/753-ask-37signals-how-do-you-process-credit-cards) back in 2007. If merchants didn't want to connect their customers to Paypal to checkout, they had create a Merchant Account, followed by having an account with Authorize.net, and then an engine called ActiveMarchant with is Ruby gem for reoccuring billing - which required alot of custom code and an intense daily business process.
One thing that was'nt mentioned was security - Payment Card Industry Security Standard (PCI Compliance). This means that if you do take credit card information via your website, your site must be sercured, for example. And if you store credit card data, it must be encrypted, etc.

###Stripe
[Stripe](https://stripe.com), luckily has changed all that as one of the first pioneers in this line of payment processing. You don't need a merchant account or gateway - which is a huge advantage.

Stripe also has the advantage of having a test mode that will allow you to test the payment pipeline while in the prototyping phase. Stripe will connect to your bank account and submit funds recieved on a 7 day rolling basis.

####Stripe.js
If you incorporate Stripe.js in your payment processing, the clients credit card info is never touches your website, it goes straight to Stripe and token is returned. This also allows us to bypass the PCI Compliance standards. The cost is also pretty negligible to have a robust, reliable, safe payment processing system for your online business.

##How to create a payment with Stripe

Select Test mode, once signing up

Include Stripe gem and bundle install

Specify your API key, use your Test Secret Key and set it a local variable in your rails console.

"Stripe.api_key = '4uhsvjfudjpsdf0u fvpdapo' "

Grab Fake Charge Data from Stripe API docks:
Copy code block and enter in Rails console
The Charge object, along with attributes will be returned once process.

Remember: currency is always in cents and you don't have to collect address information from customers when using Stripe

Refresh and you should see the charge that was just submitted in your console.

##Accept Payments with Checkout
How to get the credit card information from customers to the web application

###Using Stripe Checkout Buttom
is simply done with a button and you can add text, as well.
{%highlight ruby%}

#views/payments/new.html.haml
%p Thank you for you support.
= form_tag payments_path do
  %script.stripe-button{"data-amount" => "2000", "data-description" => "2 widgets ($20.00)", "data-image" => "/128x128.png", "data-key" => "pk_test_------------------------", "data-name" => "Demo Site", :src => "https://checkout.stripe.com/checkout.js"}

{%endhighlight%}

Update the following fields:
Data amount, Data-name, Description, and remove logo


####Create routes
Create '/payements', new and create routes and grab code from API docs

####If you run binding.pry in the payments#create action
You will see that one of the params submitted to your server is the stripe token - which is the only indication that credit card info has been submitted.
{%highlight ruby%}
class PaymentsController < ApplicationController

def create
  Stripe.api_key = "sk_test_FKBLHWUFHPWHFO29793772"

  token = paramns[:stripeToken]

  begin
    charge = Stripe::Charge.create(
      :amount => 3000,
      :currency => "usd"
      :card => token
    )
    flash[:success] = "Thank you for your payment"
    redirect_to new_payment_path

  rescue Stripe::CardError => e
    flash[:error] = e.message
    redirect_to new_payment_path
    end
  end
end
{%endhighlight%}

##Accept Payments with Custom Form using Stripe.js
Sometimes using the Stripe Checkout button is not seamless enough and you want submit payments through a custom form, where the payment form is apart of the UI:

###1. Add Stripe at the top of the views template and set publishable key

{%highlight ruby%}

<script type="text/javascript" src="https://js.stripe.com/v2/"></script>

<script type="text/javascript">Stripe.setPublishableKey("#{ENV['STRIPE_PUBLISHABLE_KEY']}");
</script>

= javascript_include_tag 'payment'
{%endhighlight%}

The above code also includes a turns off the submit button once its clicked to disable multiple submissions and then submit the custom form data to stripe for the stripe token and name attributes are set to nil, which means the data being submit will not be posted to the database. A hidden field for the StripeToken is generated and then submited to our server.


You should also create a file config/initializers/assets.rb with the following content (assuming your javascript file is called payments.js):

Rails.application.config.assets.precompile += ['payments.js']
This file will help to make sure that you add the payments.js file in the asset pipeline for all environments (dev, test and production), although in the development environment it doesn't really compress the assets for you.

Note: If you are using CircleCI at this point, your build may fail and you may need to set your stripe key environment variable on the CircleCI server as well, so it'll have access to it. (Project Settings -> Environment Variables)








