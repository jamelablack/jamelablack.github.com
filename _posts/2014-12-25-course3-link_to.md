---
layout: post
title: "Build Robust & Production Quality Applications: Precourse, Link_to Review"
tags: tealeaf
---
This is a quick reivew of how to list all a catgories videos:

{%highlight ruby%}
- @category.videos.each |video| do
    link_to video, do
      %img(src="#{video.small_cover_url}")

- @category.videos.each |video| do
    link_to video_path(video), do
      %img(src="#{video.small_cover_url}")

- @category.videos.each |video| do
  link_to image_tag(video.small_cover_url), video_path(video)

- @category.videos.each |video| do
  link_to image_tag(video.small_cover_url), video
{%endhighlight%}
