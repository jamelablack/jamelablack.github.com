---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 1: Git Flow"
tags: tealeaf
---

How to create a branch and checkout at the same time:
{%highlight ruby%}
git checkout -b mod3
{%endhighlight%}

1. Anything in the master branch is deployable
2. To work on something new, create a descriptively named branch off of master (ie: new-oauth2-scopes)
3. Commit to that branch locally and regularly push your work to the same named branch on the server
4. When you need feedback or help, or you think the branch is ready for merging, open a pull request
5. After someone else has reviewed and signed off on the feature, you can merge it into master
6. Once it is merged and pushed to ‘master’, you can and should deploy immediately

Read more: [A successful Git branching model](http://nvie.com/posts/a-successful-git-branching-model/)
