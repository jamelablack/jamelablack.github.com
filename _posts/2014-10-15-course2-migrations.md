---
layout: post
title: "Rapid Prototyping with Rails: Lesson 2, Migrations"
tags: tealeaf
---

#Items Covered:
1. Changes to Schema
2. data definition statements vs query statements
3. setting up db and creating migration
4. rolling back
      - up/down methods
5. How does Rails keep track of migrations we have run
6. When to modify a migration
7. What happens if I can't run migrations cleanly from end to end



##Data Definitions
Data Definitions are to databases what metaprogramming is to code.
* statement that alter the database rather than query statements that manipulate the
data within a database
* the steup commands before we can even query the database
* statements that alter the metadata and schema
* these occur outside of the code (kinda like setting up the spreadsheet before entering data)
* migrations are a good example of this

##Migrations
We never want to push our database to other developers when working on a team,
we want to push our schema however, and this is done in the form of migrations.
- by default, all sqlite3 files under db will be ignored when pushing to git, as per gitignore
- To list rake commands pertaining to db:
{%highlight ruby%}
  rake -T | grep db
{%endhighlight%}

##Database Schema Migrations
- There is table called schema_migrations which stores the migrations of all currently within
your database
- rake db:rollback will roll back you most recently ran

##Rollbacks
**Have you pushed the migration to github... have you shared locally?**
If not, then rollback is fine, if not.. don't. Because rollback will not create a diff file name.
The code changed and its fine on your end.. but for your team members, since the filename hasn't
changed, it will cause their application to blow-up since their migration did not re-run. Instead,
create a new migration such as rails g migration rename_users. Always google, "rails migrations methods"

**What happens if applications can't run cleanly from end-to-end?**
One of the first thing your team-members will do is run rake db:migrate when cloning your projectand migrations that don't run smoothly is a red flag.
Schema.rb
- can also be loaded when your migrations don't run from to end-to-end.
{%highlight ruby%}
   => rake db:schema:load
{%endhighlight%}
- you can search for available commands using
{%highlight ruby%}
    => rake -T | grep schema
{%endhighlight%}
