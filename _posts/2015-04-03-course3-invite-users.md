---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 6: Inviting Users- Part 2"
tags: tealeaf
---
2nd part of Workflow:
1. User gets email and selects the "Accept this invitation" link
- URL will contain special token
2. Registation form has email prefilled
3. Upon sign up, user automatically follows recommender and vice versa

Another issue with the current form is when the invitee signs up, we lose the
 lose inviter's info and they are not not following the inviter
 - to fix this, we need a hidden field on the invitee registration form.
 - this hidden field is a field tag, rather than model backed becasue we don't want this
 token to be stored under the user's token.
