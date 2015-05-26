---
layout: post
title: "Rapid Prototyping with Rails: Lesson 2, Walk-through (model-backed forms)"
tags: tealeaf
---
##Rails Model Backed Forms - Create a Resource called: Dog


Command to change port: rails s -p 3001

1. Run migration
{%highlight ruby%}
  rails g create_dogs

    class CreateDogs < ActiveRecord::Base
      def change
        create_table :dogs do |t|
          t.string :name
          t.integer :height_in_inches, :weight_in_lbs
        end
      end
    end
{%endhighlight%}

2. Create dog.rb under Models
{%highlight ruby%}
class Dogs < ActiveRecord::Base
end
{%endhighlight%}


3. rake db:migrate or bundle exec rake:db migrate

4. Hop into Rails Console to add instances of Dog class

5. Create Resources or Routes manually
{%highlight ruby%}

1. Create Using Resources
    resources :dogs, except [:destroy]
2. Write routes manually in routes file
    get '/dogs/', to: 'dogs#index'
    get '/dogs/:id', to: 'dogs#show'
    get 'dogs/new', to: 'dogs#new'
    post '/dogs',   to: 'dogs#create'
    get '/dogs/:id/edit', to: dogs#edit
    patch '/dogs/:id', to: dogs#update
    delete  'dogs/:id', to: dogs#destroy
{%endhighlight%}

6. Create Dogs Controller
{%highlight ruby%}
#dogs_controller.rb
class DogsController < ApplicationController
  def index
  end
end
{%endhighlight%}
7. We will get an error because there no index.html.erb under the dogs folders within
the views folder


8. Create instance variable to pass into the template
{%highlight ruby%}
def index
  @dogs = Dogs.all
 end
{%endhighlight%}

9. Better yet, in your model, you could:
{%highlight ruby%}
  class Dog < ActiveRecord::Base
    def info
      "#{self.name} is #{self.height_in_inches} inches tall and weighs #{self.weight_in_lbs} lbs."
    end
  end
{%endhighlight%}

  Something like the above is okay, but not okay to add html to that statement. In no way should you ever add presentational concerns at the model level. Application_helpers is for formatting logic on the user interface

10. A better solution is to add this code to the application_helper.
{%highlight ruby%}
#application_helper.rb
    def dog_info(dog)
      "#{dog.name} is #{dog.height_in_inches} inches tall and weighs #{dog.weight_in_lbs} lbs."
    end
{%endhighlight%}

{%highlight ruby%}
#index.html.erb
    <h2>All the dogs </h2>

    <ul>
    <% @dogs.each do |dog| %>
      <li><%= dog_info(dog) %></li>
    <% end %>
    </ul>
{%endhighlight%}

_______
##Lets add the new action to the controller:

1. Add link to navigation bar for a new dog:
{%highlight ruby%}
  <div class='nav_item'>
    <%= link_to 'New Dog', new_dog_path, class: 'btn btn-success btn-small' %>
  </div>
{%endhighlight%}

2. Must update routes to include the named route:
{%highlight ruby%}
    get '/dogs/', to: 'dogs#index'
    get '/dogs/:id', to: 'dogs#show'
    get 'dogs/new', to: 'dogs#new', as: 'new_dog'
    post '/dogs',   to: 'dogs#create'
    get '/dogs/:id/edit', to: dogs#edit
    patch '/dogs/:id', to: dogs#update
    delete  'dogs/:id', to: dogs#destroy
{%endhighlight%}

3. You must list routes in proper order - order matters
{%highlight ruby%}
    get 'dogs/new', to: 'dogs#new', as: 'new_dog'
    get '/dogs/', to: 'dogs#index' as: 'dogs'
    get '/dogs/:id', to: 'dogs#show'  as: 'dog'
    post '/dogs',   to: 'dogs#create'
    get '/dogs/:id/edit', to: dogs#edit
    patch '/dogs/:id', to: dogs#update
    delete  'dogs/:id', to: dogs#destroy
{%endhighlight%}
4. Create
{%highlight ruby%}
#new.html.erb
    <h4> Create a new dog </h4>

    <%= form_for @dog do |f| %>
      <%= f.label :name %>
      <%= f.text_field :name %>
      <br/>
      <%= f.label :weight %>
      <%= f.text_field :weight_in_inches %>
      <br/>
      <%= f.label :height %>
      <%= f.text_field :height_in_inches %>
      </br>
      <%= f.submit "Create dog" %>

    <% end %>
{%endhighlight%}

##Create
{%highlight ruby%}

def create

  @dog = Dog.new(params.require(:dog).permit!)

    if @dog.save
      flash[:notice] = "You created a dog!"
      redirect_to dogs_path
    else
      render :new
    end
  end
end
{%endhighlight%}


Render can take a string or a method. Form_for looks at @post  and determines are you a new post or are you an existing post. If it is in a existing object, then the _method is injected to execute a patch, rather than post.  Params is a nested hash.

##Edit
{%highlight ruby%}

#dogs_controller.rb
def edit
  @dog = Dog.find(params[:id])
end
{%endhighlight%}

{%highlight ruby%}
edit.hmtl.erb

    <h4> Create a new dog </h4>

    <%= form_for @dog do |f| %>
      <%= f.label :name %>
      <%= f.text_field :name %>
      <br/>
      <%= f.label :weight %>
      <%= f.text_field :weight_in_inches %>
      <br/>
      <%= f.label :height %>
      <%= f.text_field :height_in_inches %>
      </br>
      <%= f.submit "Create dog" %>
    <% end %>
{%endhighlight%}


##Update
{%highlight ruby%}

def update
@dog = Dog.find(params[:id])

    if @dog.update?
      flash[:notice] = Your dog has been updated!
      redirect_to dogs_path
    else
      render :edit
    end
  end
end
{%endhighlight%}


##Resources
So all the routes from below:
{%highlight ruby%}
    get 'dogs/new', to: 'dogs#new', as: 'new_dog'
    get '/dogs/', to: 'dogs#index' as: 'dogs'
    get '/dogs/:id', to: 'dogs#show'  as: 'dog'
    post '/dogs',   to: 'dogs#create'
    get '/dogs/:id/edit', to: dogs#edit
    patch '/dogs/:id', to: dogs#update
    delete  'dogs/:id', to: dogs#destroy
{%endhighlight%}

Can be replaced with resources :dogs

##Add an edit link to the index page:
{%highlight ruby%}
index.html.erb
    <h2>All the dogs </h2>

    <ul>
    <% @dogs.each do |dog| %>
      <li><%= dog_info(dog) %></li><%= link_to '[edit]', edit_dog_path(dog)%></li>
    <% end %>
    </ul>
{%endhighlight%}

##Extract common code from new and edit forms into partial:
{%highlight ruby%}
_form.html.erb
    <%= form_for @dog do |f| %>
      <%= f.label :name %>
      <%= f.text_field :name %>
      <br/>
      <%= f.label :weight %>
      <%= f.text_field :weight_in_inches %>
      <br/>
      <%= f.label :height %>
      <%= f.text_field :height_in_inches %>
      </br>
      <%= f.submit "#{@dog.new_record? ? 'Create' : 'Update'}dog" %>
{%endhighlight%}

{%highlight ruby%}
new.html.erb
<h4> Create a Dog </h4>
<%= render 'form' %>
{%endhighlight%}

{%highlight ruby%}
edit.hmtl.erb
<h4> Edit a Dog </h4>
<%= render 'form' %>
{%endhighlight%}

##Validations

{%highlight ruby%}
dog.rb
validates :name presence: true
validates :height_in_inches presence: true
validates :weight_in_lbs presence: true
{%endhighlight%}

##rails console
Failure to satisfy strong_paramters will generate errors. There are several methods
we can call regarding errors in our rails console
{%highlight ruby%}
  dog = Dog.new(), then dog.save will generate the errors array on the object itself

  1. dog.errors #will list errors
  2. dog.valid? # will be false because there were errors
  3. dog.errors.empty? #false
  4. dog.errors.full_messages #will display an array of current error messages that you can use to display on the user interface
{%endhighlight%}

##Display errors in _form.html.erb
{%highlight ruby%}
_form.html.erb
  <% if @dog.errors.any? %>
  Please correct the following errors:

  <ul>
    <% @dog.errors.full_messages.each do |msg|
    <li><%= msg %></li>
    <% end %>
    </ul>
  <% end %>

<%= form_for @dog do |f| %>
  <%= f.label :name %>
  <%= f.text_field :name %>
  <br/>
  <%= f.label :weight %>
  <%= f.text_field :weight_in_inches %>
  <br/>
  <%= f.label :height %>
  <%= f.text_field :height_in_inches %>
  </br>
 <%= f.submit "#{@dog.new_record? ? 'Create' : 'Update'}dog" %>
{%endhighlight%}

##Field with errors
{%highlight ruby%}
When an 'form_for' is submitted with errors, automatically insert a div called 'field_with_errors' around those fields with errors to allow for styling. This will also cause the form fields to appear wider apart (padding)
{%endhighlight%}


