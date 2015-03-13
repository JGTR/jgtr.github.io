---
layout: post
title: "The stucture of a Sinatra App"
date: 2013-06-23 16:07
comments: true
categories: sinatra, ruby, dashing, heroku
---

[Sinatra](http://www.sinatrarb.com) is a framework built on Ruby to create web applications.

In this post I want to outline how the codebase of a Sinatra application is organized, given that I will start building on Sinatra soon.

### The Setup

First I have to find an interesting Sinatra app to analyze. There are several websites where lists of Sinatra apps can be browsed. I used the following sources:

  - [Sinatra website](http://www.sinatrarb.com/wild.html)
  - [Oren's Github Sinatra-examples repository](https://github.com/oren/sinatra-examples)
  - [Sinatra Dashboard App called Dashing](http://shopify.github.io/dashing/)

I chose to focus on Dashing because it may prove useful on an app that my [Flatiron School](flatironschool.com) team is working to build. (More on that later!)

The instructions for setting up the app are relatively stratight-forward.

{% codeblock lang:ruby %}
$ gem install dashing
$ dashing new sweet_dashboard_project
$ bundle
$ dashing start
{% endcodeblock %}

With just these few lines on the command-line, the rack server gets going and shows a pretty "dashing" dashboard landing page.

I then decided to post this to Heroku so it would be visible beyond my own localhost. To do this, I need to:

1. install the Heroku gem
2. make my "sweet_dashboard_project" a git repo
3. create a Heroku app
4. push the code to Heroku for deployment.

All this is surprisingly easy, and only requires the following commands:

{% codeblock lang:ruby %}
$ gem install heroku
$ git init
$ git add .
$ git commit -am "first commit"
$ heroku create
$ git push heroku master
{% endcodeblock %}

Now you can visit the resulting Dashing app here: [Sample app](http://secret-garden-3665.herokuapp.com/sample)


### The codebase organization for Dashing

The codebase is organized into the following folders:

- **assets** : includes sub-folders for fongs, images, javascript and stylesheets
- **dashboards** : has the ERB files that render into HTML
- **jobs** : has 4 ruby files that actually have the logic of the app -- the ERB files from dashboard make ruby commands that access this code
- **lib** empty directory; in other Sinatra apps, the lib 
- **public** contains the 404 error page and the favicon
- **widgets** the other meaty folder besides the jobs; contains coffeescript, html and scss files for the dashboard widgets which are rendered into the layouts in the dashboards ERB files

The other file of note is the config.ru in the root directory; this initializes the rack app.

So basically, the **views** are in the dashboards, and this pulls logic from the jobs folder (**controller of sorts**), and draws formatting/browser behavior from the widgets folder.

One notable missing element from this webapp is any need for a database / models. This is something which is necessary for most webapps and which I will have to incorporate into my Sinatra app to mold it to my purposes. More about that later!