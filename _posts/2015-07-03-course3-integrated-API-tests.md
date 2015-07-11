---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 8: Fully Integrated API Tests"
tags: tealeaf
---

The are generally two options when testing third party API wrappers. We could only go to the where the TPA is and call the API and create fake response data - so what  we are testing is given the generated response, can your application interpret the response and generate the desired result. On the other hand, we can go all the way to the extent to hit the TPA server and hit their server, getting the real response and intergrate our wrapper code with that data to test it.

In this instance, we are writing fully intergrated API specs
require 'spec_helper'

describe StripeWrapper::Charge do

  before do
    StripeWrapper.set_api_key
  end

  let(:token) do
    Stripe::Token.create(
      :card => {
      :number => card_number,
      :exp_month => 7,
      :exp_year => 2016,
      :cvc => 390
        }
      ).id
  end


  context "with a valid card" do
  let(:card_number) { '4242424242424242' }

    it "charges the card successfully" do
    response = StripeWapper::Charge.create(amount: 300, card: token)
    expect(response).to be_successful
    end
  end


  context "with invalid card" do
  let(:card_number) { '4000000000000002' }
  let(:response) {StripeWrapper::Charge.create(amount: 300, card: token) }

  it "does not charge the card successfully" do
    expect(response).not_to be_successful
  end

  it "contains an error message" do
    expect(response).to eq('Your card was declined')
    end
  end
