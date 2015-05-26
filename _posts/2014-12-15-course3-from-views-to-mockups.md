---
layout: post
title: "Build Robust & Production Quality Applications: Precourse, Views to Mockups"
tags: tealeaf
---

##The Process of Building Queues from Mockups:
###Building out the videos page
####Step 1. Copy layout from the ui videos template
{%highlight ruby%}
%article.video
  .container
    .row
      .video_large_cover.col-sm-7.col-sm-offset-1
        %img(src="http://dummyimage.com/665x375/000000/00a2ff")
      .video_info.col-sm-3
        %header
          %h3 Futurama
          %span Rating: 4.5/5.0
        %p Pizza boy Philip J. Fry awakens in the 31st century after 1,000 years of cryogenic preservation in this animated series. After he gets a job at an interplanetary delivery service, Fry embarks on ridiculous escapades to make sense of his predicament.
{%endhighlight%}
####Step 2. Change/Remove hardcoded data for future code
{%highlight ruby%}
%article.video
  .container
    .row
      .video_large_cover.col-sm-7.col-sm-offset-1
        %img(src="#{@video.large_cover_url}")
      .video_info.col-sm-3
        %header
          %h3= @video.title
          %span Rating: 4.5/5.0
        %p= @video.description
{%endhighlight%}
####Step 3. Build Routes corresponding with future code
{%highlight ruby%}
Myflix::Application.routes.draw do
  resources :videos, only: [:show]
 end
{%endhighlight%}

####Step 4. Begin testing videos controller spec
