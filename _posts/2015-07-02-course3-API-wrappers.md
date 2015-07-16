---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 8: How to Build API Wrappers"
tags: tealeaf
---
Since we are going to be using Stripe alot, it may be smart to put the API in something called an API Wrapper, which becomed a centralized place for the API, rather than spread through-out your application.

Create a Wrapper called Stripe Wrapper
#models/stripe_wrapper.rb
module StripeWrapper
  class Charge
    def self.create(options={})
      Stripe::Charge.create(amount: options[:amount], currency: 'usd', card: options[:card])
    end
  end
end


Then we want to create a seperate method within the Charge class for the Stripe API Key because there may be other processes that need rely on the key. We want want to write a conditional that would show the write key according to right env we are in, production, dev, test. etc.

module StripeWrapper
  class Charge
    def self.create(options={})
      StripeWrapper.api_key
      Stripe::Charge.create(amount: options[:amount], currency: 'usd', card: options[:card])
    end

    def self.set_stripe_api_key
      Stripe.api_key = Rails.env.production? ? ENV['STRIPE_PUBLISHABLE_KEY'] : "pk_test_Xxzh18R0rGLpYys1rVT4KHLH"
    end
  end
end

This handles the first part of the API request, but we also need to address the response:

Now we can update the PaymentsController to:
class PaymentsController < ApplicationController
def create
  token  = params[:stripeToken]

  charge = StripeWrapper::Charge.create9:smount => 3000, :card => token)

    if charge.successful?
      flash[:success] = "Thank you for your generous support!"
      redirect_to new_payments_path
    else
      flash[:error] = charge.error_message
      redirect_to new_payment_path
    end
  end
end

Update the StripeWrapper

module StripeWrapper
  class Charge
    attr_reader :response, :status

    def initialize(response, status)
      @response = response
      @status = status
    end

    def self.create(options={})
      StripeWrapper.api_key
      begin
        response = Stripe::Charge.create(amount: options[:amount], currency: 'usd', card: options[:card])
        new(response, :success)
      resuce Stripe::CaredError => e
        new(e, :error)
      end
    end

    def successful?
      status == :success
    end

    def error_message
      response.error_message
    end

    def self.set_stripe_api_key
      Stripe.api_key = Rails.env.production? ? ENV['STRIPE_PUBLISHABLE_KEY'] : "pk_test_Xxzh18R0rGLpYys1rVT4KHLH"
    end
  end
end

