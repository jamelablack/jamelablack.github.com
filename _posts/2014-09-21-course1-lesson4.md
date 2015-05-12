---
layout: post
title: "Intro to Ruby & Web Dev: Lesson 3"
tags: tealeaf
---
#Items covered:
1. Ajax
2. Un-obstrusive Javascript
3. Jquery
4. Deployment Options


##Ajax
is used in a scernario when elements on the page are changing but you only want thos elements
to change rather reload (reconstituting the state of your application) each time.
Pages can be computationally expensive and create a bad user interface.
Ajax will issue the request behind the scences and the when the response comes back, your Javascript code must handle that data.
This is using unobstrusive Javascript. For example, if you have likes on your Facebook page, we will want
to see an increment in the number of likes and the like button go from "like" to "unlike".
However, for our use case, only the URL won't change.

##Un-obsturive Javascript
Back in the day, Js used to be added directly to the HTML but now a js file is created just like
the stylesheet.css file added to the layout.erb
JQuery is now the defacto Js Library.
{%highlight html%}
You must add the <script src="/application.js"></script> to the layout.erb
Google hosts Jquery for you!
1.x snippet: <script src="//ajax.googleapis.com/ajax/libs/jquery/1.11.1/jquery.min.js"></script>
{%endhighlight%}

- # is an anchor tag
##JQuery
.class - same style
 #id - unique element on your DOM
 {%highlight js%}

 $(document).ready(funtion(){
    $("element").click(funtion() {
        $(#player)_area).css('background-color', 'yellow')
        return false;===> do not re-execute
      });
  });
{%endhighlight%}

un-obstrusive Js allows you to hijack the webpage.
You shouldn't create elaborate pages with a bunch of event-listeners.
Using JQuery in this manner can be hard to manage. So if you're looking to create an enriched
user-experience, you should use Angular, Backbone, and/or Ember.
So more like dashes and sprinkles of Jquery rather than overload of JQuery

Hijacking (Adding JQuery to) the "hit-button"

{%highlight js%}

$(document).ready(function() {
  $('#hit_form input').click(function() {
    alert('hit button clicked!');
    return false;
    });
  });
{%endhighlight%}
use console.log (to inspect/debug in Firefox)


Using Ajax
{%highlight js%}
$(document).ready(function() {
  #$('#hit_form input').click(function() { #must be rebind with the input again after indcating layout false
    $document).on(click, '#hit_form input', function()) {

    $.ajax({ #this is a hash, much include comma after entry
      type: 'POST',
      url: '/game/player/hit'
      #data: #also known as parameters
      )}.done(function(msg) {
        #$('#game').html(msg); #create div for game. Can't use HTML b/c its creating a duplicate game div id
        $('#game').replaceWith(msg);
        });
      });

    return false;
    });
  });
{%endhighlight%}


##Deployment Options
1. Self-managed
* Shared host (no longer popular)
* VPS (most popular)
  - AWS, Rackspace, Linode, Digital Ocean
  - must have sys admin knowledge
* Dedicated Server (hardware) - most expensive

2. Managed - no sys admin needs, no access to file system
* Heroku
* Google App Engine
* Ninefold


