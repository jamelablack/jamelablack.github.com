---
layout: post
title: "Rapid Prototyping with Rails: Lesson 2, Nested Routes"
tags: tealeaf
---
Comments - using Nested Routes

{%highlight ruby%}

  def create
    @post = Post.find(params[:post_id])
    @comment = Comment.new(params.require(:comment).permit(:body))
    @comment.post = @post
  end
{%endhighlight%}

  OR

{%highlight ruby%}
  def create
    @post = Post.find(params[:post_id])
    @comment = @post.comments.build(params.require(:comment).permit(:body))
  end
{%endhighlight%}

What we've covered:

1. Creating a Nested Resource

2. Creating a model-backed form in a nested resource way... to generate a nested URL

3. In your controller Create action, you must set up the parent object in the controller action, so that it can be called on in your form_for

4. Under the error condition, you have to render 'posts/show' rather than 'new'
