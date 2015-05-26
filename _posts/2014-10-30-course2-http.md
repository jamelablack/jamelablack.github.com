---
layout: post
title: "Rapid Prototyping with Rails: Lesson 3, HTTP"
tags: tealeaf
---
##HTTP has two parts:
###1. Request:
* Verb/Method: GET, POST
* URL
* Parameters

###2. Response:
1. Status Code
  * 200 Ok
  * 302 Redirect (issues another request)
  * 404 File Not Found
  * 500 Application Error
2. Payload/Message/Body - which will depend on the request

Modern browsers will block against redirect loops and/or limit redirects.

A request is not connected to another request - meaning its stateless.
{%highlight ruby%}
An example:
GET/new_game
  - display the form to enter your name
  - render a view: 200 and HTML (complete the response)

POST/new_game
  - save the name somewhere (persistence layer)
  - display the next thing
  - redirect to another URL: 302 and the next URL completes the response.

Persistency Layer
1. Database (most common)
2. Session cookies (persistency within your browser)

{%endhighlight%}
