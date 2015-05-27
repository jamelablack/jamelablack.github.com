---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 6: ActiveSupport::Concerns"
tags: tealeaf
---
In our Todos example, we created a method
{%highlight ruby%}
before_create :generate_token
{%endhighlight%}
for and then defined the generate_ token method under private,
within the Todos model.

But what if you want to include this in several models?
DRY does not only mean "Dont Repeat Yourself", but it also means to have
one authoritative source for method calls

That's where ActiveSupport::Concerns can help by basically extracting the common code into a module

{%highlight ruby%}
lib/tokenable.rb
module Tokenable
extend ActiveSupport::Concern

  included do
    before_create :generate_token
  end

  def generate_token
    self.token = SecureRandom.urlsafe_base64
  end
end
{%endhighlight%}

Then in the Todo model:
{%highlight ruby%}
class Todo < ActiveRecord::Base
  include Tokenable
{%endhighlight%}

With Rails 3 or later, the lib directory is no longer automatically loaded
You instead have to:
{%highlight ruby%}
require_relative '../..lib/tokenable'

class Todo < ActiveRecord::Base
  include Tokenable

end
{%endhighlight%}
Now if you dont want to require_relative everytime:
You can do one of the two:

1. move the Tokenable file to your models folder
2. go to /enviornments/application.rb
  config.autoload_paths << "#{Rails.root}/lib"

then remove require_relative from the model.
