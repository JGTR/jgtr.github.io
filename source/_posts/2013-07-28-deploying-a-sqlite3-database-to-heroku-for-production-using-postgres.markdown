---
layout: post
title: "Deploying a Sqlite3 database to Heroku for production using Postgres"
date: 2013-07-28 23:21
comments: true
categories: sinatra, rails, database, sqlite3, postgres, heroku
---

In this post I want to discuss how we deployed to production on Heroku despite having applications running sqlite databases.

### The problem

Sqlite is a very easy database to setup, and hence, it has been the starting point for the projects I have worked on at the [Flatiron School](http://flatironschool.com). These include: 

  - [Kegkong](http://www.kegkong.com): an Arduino-powered flow-meter for the Keg, with a Sinatra web-interface
  - [InstaQuiz](http://vast-ravine-3721.herokuapp.com): a Ruby on Rails web application to empower teachers and students to collaborate in the creation of class quizzes
  - [Mission](http://www.missionapp.co): The webpage of an iPhone app I have been working on, that will facilitate discovery and participation on Instagram photo contests.

The natural evolution of development is to want to deploy to a server on the web and link the project to a meaningful domain name such as www.kegkong.com, by using a CNAME.

(There is definitely a place for taking control of your server and using something like [Digital Ocean](http://www.digitalocean.com), however, [Heroku](http://www.heroku.com) is my preferred service for quick deployment.)

One problem my teams have run into when trying to deploy to Heroku, is that Heroku no longer supports sqlite as a production database. This is troublesome because in the development of our projects, there is a significant amount of data that we have generated which we would like to maintain. This is also somewhat of an inconvenience because we have not been fully versed in the use of other more production-ready databases.

I will assume for this post that the reader is already comfortable with deploying to Heroku and will focus on the database aspect.

### Deploying to Heroku
Once you have an account with Heroku, and have initialized the git repository, it is very easy to deploy the application from your project folder on your console. The commands are:

{% codeblock lang:bash %}
heroku create
git push heroku master
{% endcodeblock %}

This will provide you with a temporary url, such as: [http://vast-ravine-3721.herokuapp.com](http://vast-ravine-3721.herokuapp.com).

If your application is running on sqlite3, Heroku will generate errors telling you this isn't working.

### Enabling postgres for production

Now you need to tell your application to use postgres when in production. This requires the use of the 'pg' gem, and code as follows in your Gemfile:

{% codeblock lang:ruby %}
group :development do
  gem 'sqlite3'
end

group :production do
  gem 'pg'
  gem "activerecord-postgresql-adapter"
end
{% endcodeblock %}

You must also setup a config directory and a database.yml file in it to tell postgres the credentials to your database. Mine looks like this:

{% codeblock %}
development:
  adapter: sqlite3
  database: database.db

test:
  adapter: sqlite3
  database: database.db

production:
  adapter: postgresql
  database: mission
  username: username
  password: password
  host: localhost
{% endcodeblock %}

If you are using Sinatra, you must tell the App to establish a connection between Activerecord and your database, otherwise you will not be able to run your migrations. Add this code to your app.rb or custom environment.rb file:

{% codeblock lang:ruby %}
configure :development do
  set :database, "sqlite3:///database.db"
end

configure :production do
  db = URI.parse(ENV['DATABASE_URL'] || 'postgres://localhost/mydb')

  ActiveRecord::Base.establish_connection(
    :adapter  => db.scheme == 'postgres' ? 'postgresql' : db.scheme,
    :host     => db.host,
    :username => db.user,
    :password => db.password,
    :database => db.path[1..-1],
    :encoding => 'utf8'
  )
end
{% endcodeblock %}

When I ran this code, Heroku would still be unable to open a link to the database server. To fix this, I had to add a heroku add-on, and manually activate a postgres database.

Do this at the following link: https://addons.heroku.com/heroku-postgresql . You can select the free database plan and select your application. Heroku then gives you a path name for your database. In my case, this was: HEROKU_POSTGRESQL_COBALT_URL .

Now replace the line 'DATABASE_URL' with the path name, and your Heroku application should recognize the database.

Now you redeploy your code to Heroku, and this time there should be no errors associated to sqlite3.

### Setting up your database on Heroku

Although the application now recognizes a postgres database, it still does not have the proper tables and data required to function properly.

The best solution I have found thus far, is to reinitialize the database using seed data. I will discuss why merely transferring data from your development environment to Heroku has not worked for me at the end of the post.

First, you need to setup your tables, to do this you can still use (a soon to be deprecated Heroku command):

{% codeblock lang:bash %}
heroku rake db:migrate
{% endcodeblock %}

After that you seed your shiny and new postgres database:

{% codeblock lang:bash %}
heroku rake db:seed
{% endcodeblock %}

And lastly you can test that your database is up and running with the right data via the Heroku console:

{% codeblock lang:ruby %}
heroku run rails console

User.first
=> #<User id: 1, name: "First User", email: "123@abc.com", created_at: "2013-07-29 04:03:57", updated_at: "2013-07-29 04:03:57", password_digest: "$2a$10$zqeVVZatSnlUF79zIMPUoOHysElF/XoOif.nHX9MU3al...">
{% endcodeblock %}

If there are no other errors, your application should now work as expected.

### Challenges

There were two additional problems that I had to deal with:
  
  - Transferring data from a sqlite3 database to postgres
  - Precompiling assets when the Rails app on Heroku could not locate the application.js file.

  Both of these problems remain largely unresolved, I avoided the first by restarting the database and not transferring data as I wanted to do. And I avoided the second by simply deleting the application.js file. None of these are the appropriate solutions, and so I plan to tackle these problems in my next posts.

Happy coding.








