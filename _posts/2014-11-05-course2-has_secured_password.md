---
layout: post
title: "Rapid Prototyping with Rails: Lesson 3, Working with has_secured_password"
tags: tealeaf
---
##Steps:
1. Install bcrypt-ruby gem
2. Add password_digest column to users table
3. Add has_secured_password to user model
4. Turned off validations for has_secured_password

##Creating password in console:
1. bob = User.first
2. bob.password = "password"
3. bob.password_confirmation = "password"
4. bob.save

##To check what password is in console:
You can never retrieve the password directly, but you can verify using the authenticate
{%highlight ruby%}
 bob.authenticate('password')
{%endhighlight%}
* will return False, if guess is incorrect or retrieve the user object if True.

Password Digest column includes salts to prevent against rainbow attacks.
Salts adds a small chunk of random data to password to prevent those attacks.
