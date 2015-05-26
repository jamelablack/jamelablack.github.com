---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 4: Update Review in Queue"
tags: tealeaf
---
In Myfix, we want to be able to allow the user to update video rating in the video_play queue.

Goal: To change video rating from the queue_item index.
To do so, we have to create the virtual attribute on the queue_item model, as though it is a column on the queue_item model (table). Since it is not in the model, we have to define the getter and setter.
{%highlight ruby%}
#queue_item.rb
def rating

end

def rating=(new_rating)
 -this is where you begin to write your tests
end
{%endhighlight%}
##Update Column vs Update Attribute
update_attributes will go through validation error unlike the update_column method,which will bypass validation.
{%highlight ruby%}
def rating=(new_rating)
  review = Review.where(user_id: user.id, video_id: video.id).first
  review.update_column(:rating, new_rating)
end
{%endhighlight%}
##Memoization
Memoization means the first time the record is called, it will hit the database only once and store that info
into the instance variable and will not database again when record is called.
{%highlight ruby%}
def review
  @review ||= Review.where(user_id: user.id, video_id: video.id).first
end
{%endhighlight%}
