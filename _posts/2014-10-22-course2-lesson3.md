---
layout: post
title: "Rapid Prototyping with Rails: Lesson 3, part 1"
tags: tealeaf
---

##Items Covered:
1. Quiz Lesson 2 Review
2. Asset Pipeline
3. Authentication from scratch
{%highlight ruby%}
** has_secured_password
** password_digest
** bcrypt
** validations
** helper methods
** before_action
{%endhighlight%}

##Quiz Review
* Remember that the show, update, and delete actions routes require an object and not an (:id).
{%highlight ruby%}
   post_path(post)
{%endhighlight%}

* REST is url convention pattern means that url endpoints are exposed using the resources keyword.

These urls correlate with the CRUD actions on the controller side.

* Difference b/w model and non-model backed helpers is that
{%highlight ruby%}
  - Model backed helpers are tied to an object
  - There is slight syntactical difference:
        - non-model: text_field_tag :title
        - model: f.text_field :title
{%endhighlight%}

There is no additional functionality with non-model backed forms.

Non-model forms are used for elements that are not models... such as passwords but model-backed forms are used for CRUD actions for models.


##Asset Pipeline:
obfuscates your js code to make it unreadable in production (to prevent unallowable use) and then allows for better response time bc
many browsers can only allow for 10 items to load at a time. Thats why we have to use an asset library that caches

###jammit
is a good open source obfuscator, and sprockets
  Functions:
  1. Obfusgation
  2. File compression
  3. Timestamp (Cache Buster)

Can be found under the production application -> public/assets folder
Goal is to take all the js files, obfusgate, jam it into one file under the public/assets file

###Cache buster
is the string tied to the asset level or DNS level
  -different browsers have diff means of caching and thats why Google chrome is considered to be
  faster... it's because Chrome caches agressively. The cachebuster gives a reference number in which the browser and
  application can manage/track caches to allow refresh of new information or changes.

Application.js is a manifesto file where the obfuscated js code resides (using Sprockets as the asset manager). Same goes for Application.scss
So how/does Sprockets compile your files under assets and places them under public/assests folder in production:
{%highlight ruby%}
      rake assets:precompile
{%endhighlight%}

###There are two asset management approaches:
1. As a development team,every time a change is made, run rake assets:precompile
to make changes to static assests, commit changes and move those files to our production
2. We want to delete all files under public and when we deploy, we will run rake assets:precompile at deployment only and then that will
 take our code and turn into static assets.

 ###Which is better:
 Option 2 is better but your deployment could take longer (maybe 5 mins)
 We will be following Option 2 and waiting to precompile bc Heroku does it for us.
 We should delete the public/assets folder. If we don't, Heroku will delete the rake assets:precompile command
 and when changes are made, they will not be reflected in your production code.

 Another common problem is when running locally, Js files will be picked in both the application.js file
 and the assets folder therefore in our case.. when we use js to build our voting functionality, you will have to vote twice
 This is all solved if you remove your public/assets directory


##How password authentication works!
Passwords should never be saved in your application, in fact, your application should never know your password
and shouldn't be able to send you your 'forgotten password' via email.

How passwords are saved is through a one-way hash. We hash the string (password) into some one-way
indecipherable hash token. When you attempt to enter in your set password, the string will also be converted
into a one-way indecipherable has and its determined if they match.

This password method is vulnerable to dictionary attacks/rainbow attacks, that's why they say pick a password with a capital letter
and a special character.

1. So we need a column added to the user table to store the hash token and we will call that, password_digest:
{%highlight ruby%}
  - In terminal - rails g migration add_password_to_users
  _ Open migration file:
      - def change
        add_column :users, :password_digest, :string
  - rake db:migrate
2. Go to User model and add has_secure_password
3. Add 'bcrypt-ruby' to your gemfile - contains the complex algorithms to create our one-way hash
    Use 'bcrypt-ruby', =3.0.1 if you have installation problems during bundle intall

has_secure_password
4. has_secure_password has validations functionality where password cannot be blank, so add validations: false
        has_secure_password validations: false

Tip: Use gems that do one thing really well not multiple things.

5. has_secure_password gives us multiple methods we can now use:
      1. password setter: u.password = "apple", when run u.save, you will see that password is just a
      virtual attribute, and "apple" was saved to the password_digest column and encrypted using bcrypt.
      2. There is NO getter, so you can't run u.password and expect to get the value.
      3. But there is method called u.authenticate('password'), where you pass in a password that you are trying to check against
      in the database.
       - If the password is not correct, false will be returned.
       - If correct, the user will be returned.
       Authenticate is an instance method being called on the User class
{%endhighlight%}





  -
