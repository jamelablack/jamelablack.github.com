---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 2: Generate Fake Data with Faker"
tags: tealeaf
---
##Use Faker to Generate Fake Data
Creating fake data when your model has uniqueness validations will throw errors
if your data is not unique.
{%highlight ruby%}
Fabricator(:todo) do
  name { "cooking" }
  user
end
{%endhighlight%}

{%highlight ruby%}
Fabricator(:user) do
  email { "joe@example.com"} #This will throw an error eventually if email record already exists.
end
{%endhighlight%}

###Examples

Creating a category:

{%highlight ruby%}
Fabricator(:category) do
  name { Faker::Name.name }
end
{%endhighlight%}


Creating a video:

{%highlight ruby%}
Fabricator(:video) do
  title { Faker::Lorem.words(5).join(" ") }
  description { Faker::Lorem.paragraph(2) }
end
{%endhighlight%}

Creating an invitation:

{%highlight ruby%}
Fabricator(:invitation) do
  recipient_name { Faker::Name.name }
  recipient_email { Faker::Internet.email }
  message { Faker::Lorem.paragraphs(2).join(" ") }
end
{%endhighlight%}
Read more: [Faker Docs](https://github.com/stympy/faker)
