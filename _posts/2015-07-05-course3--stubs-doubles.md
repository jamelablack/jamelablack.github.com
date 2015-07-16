---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 8: Fully Integrated API Tests"
tags: tealeaf
---

##Stub Methods

Typically, you want to use the stub method in three scenarios:
1. You want to use the stub method when actually calling a method is expensive in terms of time. So you stub the test double to be in state that you desire.


2. The action or interface that you're testing is already thoroughly tested elsewhere. For example, there is no need to fully test the Stripe API's ablility to charge credi card and return token, becasue this funcitionality is already in the StripeWrapper::Charge test - therefore stubbing would be perfectly fine.

3. You want to mimick some behavior you haven't fully developed yet.


Doubles create a fake object that stands in the place of a real object and stubs call a desried fictitious method on that double and gives it a desired value as a result of calling that fictitous method.

We are intersted in doubles and stubs in this case because we want to avoid actually submtting a an charge and hitting Stripe's server.


{%highlight ruby%}
#spec/controllers/payments_controller.rb
require 'spec_helper'

describe PaymentsController do
  describe "POST create" do
    context "with a successful charge" do
      before do
        charge = double('charge')
        charge.stub(:successful?).and_return(true)
        StripeWrapper::Charge.stub(:create).and_return(charge)

        post :create, token: '123'
      end

  it "sets the flash success message" do
    expect(flash[:success]).to  eq("Thank you!")
  end

  it "redirects to the new payment path" do
    expect(response).to redirect_to new_payment_path
  end
end

  context "with an failed charge" do
    before do
      charge = double('charge')
      charge.stub(:successful?).and_return(false)
      charge.stub(:error_message).and_return('Your card was declined')

      StripeWrapper::Charge.stub(:create).and_return(charge)
      post :create, token : '123'
    end

    it "sets the flash error message" do
      expect(flash[:error]).to eq('Your card was declined')
    end

    it "redirects to the new payment path" do
      expect(response).to redirect_to new_payment_path
    end
  end
end
{%endhighlight%}

{%highlight ruby%}
#app/controllers/payments_controller.rb
class PaymentsController < ApplicationController

def create
  token = params[:stripeToken]
  charge = StripeWrapper::Charge.create(amount: 3000, card: token)
    if charge.successful?
      flash[:succes] = "Thank you!"
      redirect_to new_payment_path
  else
    flash[:error] = charge.error_message
    redirect_to new_payment_path
    end
  end
end
{%endhighlight%}

Check out the Test Doubles [docs](https://www.relishapp.com/rspec/rspec-mocks/v/3-3/docs/basics/test-doubles) for more info. For more info on Method Stubs, with an older version on Rspec, checkout these [docs](http://www.relishapp.com/rspec/rspec-mocks/v/2-13/docs/method-stubs) also.
