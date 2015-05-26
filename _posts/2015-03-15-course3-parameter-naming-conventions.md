---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 4: Parameter Naming Conventions"
tags: tealeaf
---
##Parameter Naming Conventions
Visit Rails Guides' [Parameter Naming Conventions](http://guides.rubyonrails.org/form_helpers.html#understanding-parameter-naming-conventions.)
Use that as the starting point to construct the params that hit the server.


As you've seen in the previous sections, values from forms can be at the top level of
the params hash or nested in another hash. For example, in a standard create action for a
Person model, params[:person] would usually be a hash of all the attributes for the person to create.
The params hash can also contain arrays, arrays of hashes and so on.

Fundamentally HTML forms don't know about any sort of structured data, all they generate
is name-value pairs, where pairs are just plain strings. The arrays and hashes you see
in your application are the result of some parameter naming conventions that Rails uses.

###Basic Structures
The two basic structures are arrays and hashes. Hashes mirror the syntax used for accessing the value in params. For example, if a form contains:
{%highlight ruby%}
<input id="person_name" name="person[name]" type="text" value="Henry"/>
{%endhighlight%}
the params hash will contain
{%highlight ruby%}
{'person' => {'name' => 'Henry'}}
{%endhighlight%}
and params[:person][:name] will retrieve the submitted value in the controller.

Hashes can be nested as many levels as required, for example:
{%highlight ruby%}
<input id="person_address_city" name="person[address][city]" type="text" value="New York"/>
{%endhighlight%}
will result in the params hash being
{%highlight ruby%}
{'person' => {'address' => {'city' => 'New York'}}}
{%endhighlight%}
Normally Rails ignores duplicate parameter names. If the parameter name contains
an empty set of square brackets [] then they will be accumulated in an array.
If you wanted users to be able to input multiple phone numbers, you
could place this in the form:
{%highlight ruby%}
<input name="person[phone_number][]" type="text"/>
<input name="person[phone_number][]" type="text"/>
<input name="person[phone_number][]" type="text"/>
{%endhighlight%}
This would result in params[:person][:phone_number] being an array containing the inputted phone numbers.

###Combining Them
We can mix and match these two concepts. One element of a hash might be an array as in the previous example,
or you can have an array of hashes. For example, a form might let you create any number of addresses by repeating the following form fragment
{%highlight ruby%}
<input name="addresses[][line1]" type="text"/>
<input name="addresses[][line2]" type="text"/>
<input name="addresses[][city]" type="text"/>
{%endhighlight%}

This would result in params[:addresses] being an array of hashes with keys line1, line2 and city.
Rails decides to start accumulating values in a new hash whenever it encounters an input name that
already exists in the current hash.

There's a restriction, however, while hashes can be nested arbitrarily, only one level of "arrayness" is allowed. Arrays can usually be replaced by hashes; for example, instead of having an array of model objects, one can have a hash of model objects keyed by their id, an array index or some other parameter.
