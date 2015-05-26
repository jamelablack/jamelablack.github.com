---
layout: post
title: "Rapid Prototyping with Rails: Lesson 1"
tags: tealeaf
---
#Items covered:
1. Bundle Install/Bundle Exec
2. Creating your Rails App
3. Folder Directory
4. Rake Routes
5. Relational Databases

##Bundle Install
Before you couldn't have multiple versions of gems installed on your machine when
switching b/w projects.

Bundler creates a sandbox in your application where the proper versions of your gems
are stored.

###Bundle exec
does the samething and is used when your system level rake (rake db:migrate) for example
is not compatible with the current settings.
Therefore you have to use bundle exec rake db:migrate or just bundle exec.

Heroku also uses Bundler.

##Creating your app:
Files you should always look at:
###1. database.yml
Tells the application how to connect to the database
*In development - we use sqlite3
*In production - we use postgres (although in this course Heroku takes care it for us)


For sqlite - your database is in your code base and its one file, unlike others
where is an application and usually your database is hosted somewhere:
{%highlight ruby%}
      production
        adapter: mysql
        database: myapp_development
        host: localhost
        username:
        password:
{%endhighlight%}


You will want to mimick your local enviornment as much as possible to your production
enviornment in order to catch bugs

##2. Gemfile


###3. Routes File
How the request initally get's routed. The blueprint of your application ad its
capabilities.

##Folder Directory
{%highlight ruby%}
app - where you spend most time
  assets - where you put all static files, images, etc.
  controllers
  mailers - outbound mail. Not good to use because you can easily get flagged as spam
   (use a transactional email provider (stmp mailers) hosts such as mailgun, postmark, etc. )
   models
   views - where our erb goes
bin - where your executable code goes - we never touch this
application.rb - sets application defaults such as timezone, load path
enviornment folder - configurations for development and production, and testing.
                  - you can also create custom enviornments - such QA, intergration or staging
initializers folder - where you files that you want to load when your application
                    is initialized
Config folder
  locales - yml files - is a way to internationalize your app. Is comprised of comlicated hashes
        and you no longer write using strings. Extremely painful to manage files
Lib - you can also put assets in there
Tasks - you can place rake tasks in here
log - stores all terminal logs from your application
public - where you store your error pages
      - robosts - tell search engines where to go within your application
Vendor - a relic from the past
.gitignore - tells git what files to ignore when committing. (.files are hidden files)
Readme - is what is dispayed when you push your app to Github.
{%endhighlight%}


##Rake Routes
shows all possible endpoints in your application
You can also view your routes in your browser by going to localhost:3000/rails/info/routes
in Rails 4 only or you can use the terminal: rake routes | grep _______

##Relational Databases:
A series of spreadsheets.

To connect these tables in order to link infromation:
1. primary_key in rails is the "id"
2. foreign_key is the creation of a "user_id" column with the primary_key in on
other tables to connect to the main table.

###There are 2 Views
  1. Data View: very much like Excel
  2. Schema View: lists the column name and the data type allowed for that column.

###1:M
the Foreign_key is always on the many side of a one to many relationship

###ORMs
ActiveRecord is an ORM (Object Relational Mapper), a pattern created by Martin Fowler
which describes how to abstract data from a database into code. Prior to this, using
PHP, you would write sql code directly in the views.

In console, using "= _" is equivalent to your last returned item.

Rails is good for simple, applications with quick builds that aren't for existing
application is analytics.


Setting up 1:M b/w posts and users (Posts were previously created)

1. Create user table
Terminal: rails g migration create_users

2. Create Users model
Create model - singular, snack_case of Class name

3. Need to add foreign key to the many side (the Posts table)
Terminal: rails g migration add_user_id_to_posts

  Inside migration file:
  {%highlight ruby%}
  def change
    add_column :table_name :foreign_key (which is normally the resource+primary_key), :datatype (always will be integer)

    add_column :posts, :user_id, :integer
  end
{%endhighlight%}

4. Write in has_many and belongs_to on the models

###Virtual Attribute
attributes that are not columns, are given through associations


##Mass Assignment
The ability create/update AR obj through a hash where the keys match the attirbute
or virtual attribute.
example:
{%highlight ruby%}
Post.new(title: "some title", url: "some url", user = User.first)
{%endhighlight%}

###Post.new & Post.create
Both can be used for mass assignment. Post.new is in memory (no user id assigned)
while Post.create will update to the database immediately.

Post.build and Post.new are the same.

###When in the console:
{%highlight ruby%}
You can run the following commands:
  user = User.first
  post = Post.first
  post.user **** virtual attribute
  post.title
  post.user_id = user ***NO, you can only set objects to object and integers to integers
  post.user_id = user_id
{%endhighlight%}
