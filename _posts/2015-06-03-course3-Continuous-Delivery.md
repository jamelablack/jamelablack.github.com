---
layout: post
title: "Build Robust & Production Quality Applications - Lesson 6: Continuous Delivery"
tags: tealeaf
---

##Continuous Delivery
####Excerpt from Tealeaf:

Continuous Delivery (CD) or sometimes known as Continuous Deployment goes one step beyond CI to automatically deploy features when the new code passes the continuous integration phase. Continuous Delivery encourages small and incremental software updates over big and infrequent releases to shorten the feedback loop and fix bugs earlier.

Let's look at an example development workflow that has both CI and CD enabled, based on the Github Flow process:

** we pull the latest code from Github
** we create a new feature branch and develop a new feature
** after we finish the feature, we push it to a branch with the same name on Github
** we create a PR from this branch to the staging branch.
** we wait for the the CI server to ensure all tests pass.
** we allow the CI server to automatically deploy the code from the staging branch to our staging server
** we perform sanity tests on our staging server
** we create a PR from the staging branch to the master branch on Github
** this will trigger another round of integration and if it passes, the CI server will automatically deploy the code to the production server.


If adopted by everyone in the development team, this process will make sure our master branch is always in sync with the production server and new features are also always build on top of what's on the production server.

Assignment: Set up Continuous Delivery with Circle CI

For this assignment, you are going to enable Continuous Delivery with Circle CI.

Here are the steps:

create a staging branch locally if you don't have it yet.
in Circle CI, follow Project settings for your project and select Heroku Deployment under Continuous Deployment
configure the Heroku API key. (Get it from your Heroku account page)
associate Heroku SSH key with your Circle account so Circle can have the authority to deploy to Heroku on your behalf.
create a circle.yml file in your projects root directory and make sure your adjust production_app_name & staging_app_name to your own app name.
Here is an example of a circle.yml file that you can use:
{%highlight ruby%}
machine:
  ruby:
    version: 2.1.5
deployment:
  production:
    branch: master
    commands:
      - heroku maintenance:on --app production_app_name
      - heroku pg:backups capture --app production_app_name
      - git push git@heroku.com:production_app_name.git $CIRCLE_SHA1:refs/heads/master
      - heroku run rake db:migrate --app production_app_name
      - heroku maintenance:off --app production_app_name
  staging:
    branch: staging
    commands:
      - heroku maintenance:on --app staging_app_name
      - git push git@heroku.com:staging_app_name.git $CIRCLE_SHA1:refs/heads/master
      - heroku run rake db:migrate --app staging_app_name
      - heroku maintenance:off --app staging_app_name
Note: you should change the Ruby version to the version you're using for this project.

{%endhighlight%}

This code should be pretty self explanatory - this allows Circle to monitor your staging branch and deploy to the staging server, and monitor your master branch to deploy to the production server. It'll run migrations for you and for the production server, it also automatically backs up the database before a deploy.

Notifications Options

By default we will be notified by email on every status change of our builds. But receiving emails all the time build fails or passes might be cumbersome and there are also good alternatives.

For Mac users - there is a free application CCMenu by ThoughtWorks which we install and it will stick to our upper bar. In order to make it fully run - we will need to generate a CircleCI API key (Project settings -> API keys) and http address of build. See the setup info guide here. Great feature of this app is that it can play sounds based on build's status and we are able to customize it to our needs.

Another great feature most CIs offer are Embedded Status Badges. These are especially useful for open source projects. They look nice in README.md usually placed in GitHub repo page giving out the information about build being successful or not.


