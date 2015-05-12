---
layout: post
title: "Intro to Ruby & Web Dev: Lesson 3"
tags: tealeaf
---
#Items covered
1. HTTP Request/Response Cycle
2. MVC
3. Sinatra(Shotgun)
4. Erb
5. Persistence

#Difference b/w websites and web applications
1. Websites - static HTML, web server
2. Web Applications - Actual server

##HTTP
is broken up into 2 actions:

1. Request
  - HTTP Verbs/Method
  - URL
  - Parameters
2. Response
  - Status Code
  - Payload (not necessarily HTML all the time... could be JSON, XML)
HTTP is also stateless. B/w every request, the browser must reconsitute the world to resemeble that current state.
__________________

1. Request - any interaction that you have with a web application
  * 2 Types of Requests - this is how a user interacts with the website
  1. HTTP Verbs
    - GET
      - Link or type into address bar
      - smaller payload (there is a size limit)
      - parameters are a apart of the URL
    - POST
      - forms
      - larger payload
      - parameters are submitted in the body
    - parameters are submitted as apart of a form


  2. HTTP Methods

2. Response
  - Status Code
    - 200: ok
    - 301: redirect
    - 404: File not found
    - 500: general error

##MVC
Person visits site and a request is to the controller/actions.
Depending on the HTTP Verb, URL, and parameters passed - the controller will reference the model for the logic
and the model, in turn will potentially reach to the database. Once retrieved, the controller render a view.


We are foregoing the model and focusing on the request and render features, using a cookie as our sessions.

##Sinatra App

Sinatra uses localhost:4567

Sinatra doesn't automatically reload - for this, install shotgun and run 'ruby main.rb' => localhost:9393
###Shotgun
Allows your browser to refresh and updates with changes without stopping the server.

Install in terminal: gem install Shotgun.

Launched via port:9393 by running "shotgun main.rb"


Link is loaded at '/'

{%highlight ruby%}

In main.rb:

get '/' do
  "Hello World" +params[:some].to_s
end
{%endhighlight%}

##Erb

Instead of working with code directly - HTML, etc , we want to work with templates (.erb)

      get 'test/' do
        erb :test
      end

  - Create test.erb (layout: false) - to remove layout

Instance variables setup in the action are available in the template, but only the template is being rendered

Erb => Embedded Ruby

<% %> Ruby code that doesn't need to be printed to HTML
<%= %> Ruby code that should be printed out to HTML
____
{%highlight ruby%}
main.rb
get '/test' do
  @error = "We've got a problem"
  erb :test *** points to the test.erb template under the views***
end
{%endhighlight%}
____
##Persistance
The problem is that accessing the instance variable is only available upon request.
To display form:
{%highlight ruby%}
#main.rb
get '/' do
  erb :set_name
end
{%endhighlight%}
{%highlight ruby%}
#set_name.erb (Handles the display of data)
<h4> Set Player Name </h4>

<form action='/set_name' method='post'
  Name: <input type='text' name='player_name' />
  </br>
  <input type ='submit' class='btn'/>
</form>
{%endhighlight%}

{%highlight ruby%}
#main.rb (handle the submission of the data)

post 'set_name' do
end
{%endhighlight%}

Whenever making changes to the HTML, you must refresh the browser to take effect.

In order to fix persistence problem, you must use session instead of an instance variable. This allows use to work with cookies( has a limit of 4 kilobytes instead of dealing with relational databases. Can't deal with more than one decks.:
{%highlight ruby %}
post '/set_name' do
  session[:player_name] = params[:player_name]
end
{%endhighlight%}

Sessions can be referenced directly because of the use of cookies.
We know that cookies have tied to the session because we did not reference an instance variables.

You can use the following to reference in your templates in order to display content dynamically and reconstitute all the states you expect.
1. Instance variables
2. Sessions
3. helper methods

The key is to figure out when you will redirect or render:

* Post - typically redirect
* Get - typically render

Sinatra is typically very much to the metal and handles much less than Rails.


**NOTES FROM BLACKJACK GAME**

1. main.rb is the game engine.

2. Constants: use constants when numbers are consistent through out the the game. This will allow you to be able the rules of the game in the future without having to comb through the code to find where to make the change.

3. "set :sessions, true" facilitates some level of persistency, reconstitutes state and allows for your player info to tracked for that session.

4. Helpers - "helpers do .... end" any methods within this block will be accessible in both the game engine (main.rb) and the (.erb) templates.

5. Used @error and @success bootstrap instance variables to communicate special messages

6. Require new player to enter in name:
      post '/new_player' do
        if params[:player_name].empty?
          @error = "Name is required."
          halt erb(:new_player) #stop, don't execute anything below this, render template instead.
        end
        session[:player_name] = params[:player_name]
        redirect '/game'
      end
6. Keep track of who's turn it is with:
{%highlight ruby%}
    session[:turn] = session[:player_name]
      or
    session[:turn] = "dealer"
    {%endhighlight%}

7. Instance Variables: using instance variables allow your game engine to communicate to your layout template and the data is erased as soon as another request comes in.

8. halt erb(:new_player) #means stop, don't execute anything below this, render template instead.

9. Always include "erb :game" to render the desired template rather than creating a redirect loop.

10. When using post in main.rb and forms in .erb, you must specify the method as "post" in erb becasue the default is GET if you don't specify.
{%highlight ruby%}
    <form action='/new_player' method="post">
      <input type="text" name="player_name" />
      <br/>
      <input type="submit" class="btn btn-inverse" value="Let's Play!"/>
    </form>
{%endhighlight%}


1. You must designate method="post", the default is "get".
2. Must give the input "text" an arbitrary name, in this instance, we chose "player_name"
3.  main.rb: under post, params is reset everytime you call it, so we must set the params[:player_name] = session[:player_name]


11. There are different input types for forms including:

  input type = submit
  input type = text, name = :player_name (must specify a name to track the value)



