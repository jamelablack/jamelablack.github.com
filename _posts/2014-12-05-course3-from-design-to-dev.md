---
layout: post
title: "Build Robust & Production Quality Applications: Precourse, From Design to Development"
tags: tealeaf
---
##Items Covered:
1. Slicing Mockups
2. SASS
3. Haml

##How to serve sliced mockups pages
###Slicing with UI Controllers

We will use a UI controller that will point to pages that we need, such as "sign in" page, "account page".
Currenty the data and images in these layouts/templates are hardcoded with data and images (nothing is actually linked to the database)

Because we don't want these templates accessible in prod, we have a before filter to ensure that that's the case:
{%highlight ruby%}
#ui_controllers.rb
class UiController < ApplicationController
  before_filter do
    redirect_to :root if Rails.env.production?
  end

  layout "application"

  def index
  end
end
{%endhighlight%}

###In the routes:
{%highlight ruby%}
#routes.rb
Myflix::Application.routes.draw.do
  get 'ui/:action)' , controller: 'ui'
end
{%endhighlight%}

###UI Index
So we want our index action to show all views available with links containing ('html.haml') unless its index:
{%highlight ruby%}
%section.ui-index
  %ul
    - Dir.glob('app/views/ui/*.html.haml').sort.each do |file|
      - wireframe = File.basename(file,'.html.haml')
      -  unless wireframe == 'index' || wireframe.match(/^_/)
        %li= link_to wireframe.titleize, action: wireframe unless wireframe == 'index'
{%endhighlight%}

* To read more about UI Controllers, checkout this blog post from hashrocket:


##Stylesheets
###SASS
In this project, we will use application.css (manifest file) that will pull in all of our required files (asset pipline). You had several options - some devs like to use 1 sass/css stylesheet for their entire app, in this case, we broke it out according to the page (user sign in, billing, etc.)


There are two options when using sass, sass and scss. Scss is more verobse but desgined to be compatible with vanilla css. In this case, we are using sass because its less verobse.

* To read more about Sass:[Sass Documentation](http://sass-lang.com/guide)

##Views
###HAML
We are also using Haml instead of ERB. Haml appears to be more succint because it uses indentation instead of closing tags

* To read more about Haml: [Haml Documentation](http://haml.info/docs.html)

* To read more about the asset pipeline: [Rails Asset Pipeline](http://guides.rubyonrails.org/asset_pipeline.html#coding-links-to-assets)

