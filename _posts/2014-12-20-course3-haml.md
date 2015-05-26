---
layout: post
title: "Build Robust & Production Quality Applications: Precourse, Haml"
tags: tealeaf
---

Writing Basic Haml
1. No closing tags
2. Nesting controlled via strict white-spacing and indenting
3. Nesting tags must be indented on a new line
4. Use spaces or tabs to nest tags within other tags
5. Tabs must be consistent

Examples:
###Haml:
{%highlight ruby%}
%h2
%ul
  %li
  %li
  %li
{%endhighlight%}

###Links:
Haml:
{%highlight ruby%}
%a{href: '/courses', title: 'Courses'} Courses
{%endhighlight%}
or
{%highlight ruby%}
%a(href="/courses" title="Courses") Courses
{%endhighlight%}

HTML:
{%highlight ruby%}
<a href='courses' title='Courses'>Courses</a>
{%endhighlight%}

###Divs:
Haml:
{%highlight ruby%}
%section#content.container
  %div.stats.container
{%endhighlight%}

HTML:
{%highlight ruby%}
  <section id="content" class="container">
    <div class="stats container"></div>
    </section>
{%endhighlight%}

###Haml will insert a div if no HTML tag is explicitly defined:

Haml:
####Note: stats.container is the same as %div.stats.container

HTML
{%highlight ruby%}
<div class="stats container"></div>
{%endhighlight%}

##Adding Ruby:

* Lines to evaluated begin with a hyphen (-) instead of erb (<%  %>)
* Lines to be output begin with an equals sign (=) instead of erb (<%=  %>)

Haml:
{%highlight ruby%}
- title = 'Rails for Zombies'
%h1= title
{%endhighlight%}

Erb:
{%highlight ruby%}
<% title = 'Rails for Zombies' %>
<h1><%= title%></h1>
{%endhighlight%}

HTML output:
{%highlight ruby%}
<h1> Rails for Zombies </h1>
{%endhighlight%}

###Loops:
Haml:
{%highlight ruby%}
- zombies = %w{Ash Bob Jim}
%section#zombies
  %ul
    - zombies.each do |zombie|
    %li= zombie
{%endhighlight%}

 HTML:
{%highlight ruby%}
 <section id="zombies">
   <ul>
   <li>Ash</li>
   <li>Bob</li>
   <li>Jim</li>
  </section>
{%endhighlight%}

####You can use the same view helpers as before:
Haml:
{%highlight ruby%}
=link_to "Check us out on twitter", "http://twitter.com/codeschool"
{%endhighlight%}

HTML:
{%highlight ruby%}
<a href="http://"twitter.com/codeschool"><Check us out on twitter</a>
{%endhighlight%}

##Interpolation:

###You can also use #{} interpolation to insert Ruby expressions
Haml:
{%highlight ruby%}
%p Hi, My name is #{@user.name}
{%endhighlight%}

HTML:
{%highlight ruby%}
<p> Hi, My name is Casey</p>
{%endhighlight%}
