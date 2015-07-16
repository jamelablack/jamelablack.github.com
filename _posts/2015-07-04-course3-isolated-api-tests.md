---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 8: Fully Integrated API Tests"
tags: tealeaf
---
##Isolated API Tests Stubbing Out the Internet
Hitting the Stripe server takes alot of time approximently 22 seconds sicnce we are hitting the server twice, once to grab the token. The answer to this is pre-script a response for a given request.

###Webmock

[Webmock](https://github.com/bblimke/webmock) allows us to do this by stubbing the HTTP client at the Library level and we can tell exactly what the response is. So we can stub the response using Webmock, always setting the response to a given value, but we would have to do this for every spec. It would be great to have something that gave us this fuctionality automatically and we also don't have to change our test code...

###VCR
[VCR](http://www.relishapp.com/vcr/vcr/v/2-9-3/docs) integrates with Rspec and helps you to record your test suite's HTTP interactions and play them back automatically when you run future tests. Meaning, the first time the spec is run, the request will actually hit the TPA server, record the interaction and store it in a data file. The next time its run again... it will just play back the recorded file.

##Getting  Started

###Step 1: Install both VCR & Webmock Gems

###Step 2: Add Rspec Metadata (for VCR set up) to spec helper.rb

[Metadata](http://www.relishapp.com/vcr/vcr/v/2-9-3/docs/test-frameworks/usage-with-rspec-metadata)
{%highlight ruby%}
require 'vcr'

VCR.configure do |c|
  c.cassette_library_dir = 'spec/cassettes'
  c.hook_into :webmock
  c.configure_rspec_metadata!
end

RSpec.configure do |c|
  # so we can use :vcr rather than :vcr => true;
  # in RSpec 3 this will no longer be necessary.
  config treat_symbols_as_metadata_keys_with_true_values = true (add to Rspec config block)
end

{%endhighlight%}

###Step 3: Add :vcr to the transactions you want to record

{%highlight ruby%}
it "charges the card successfully", :vcr do
  response = StripeWapper::Charge.create(amount: 300, card: token)
  expect(response).to be_successful
end
{%endhighlight%}

###Step 4: Cassettes & Configs
As default, spec/cassettes holds all recordings and a creates data files for each spec ran. VCR allows for extensive customization. Including how many recordings per spec whether it be once or all.

###Refactor
You can add Stripe.api_key = ENV['STRIPE_SECRET_KEY'] to your initializers (config/initializers/stripe.rb), rather than including in each test.
