---
layout: post
title: "KegKong will live in the cloud"
date: 2013-07-15 09:23
comments: true
categories: KegKong, Arduino, Raspberry Pi, Flatiron School
---

Grumpycat productions presented our invention to the Rails meetup. Part of the presentation was a live demo.

We had set it up so that we could show the flowmeter take live readings and lower the keg volume as the audience drank. However, when I clicked refresh, the browser waited unbearably long. I heard nervous whistling and felt myself sweat just a little bit. We went forward with the slides and when we returned to the website a few minutes later, the measurements had been taken and the website worked. Applause was heard and relief refreshed my nerves.

We need the server to be faster.

The solution will be to deploy to Heroku. This company makes it stunningly easy to deploy a website. At low scale it is free, and they only charge for added functionality such as webworkers, or for higher scale.

Staging servers on Heroku arent that fast either, sometimes they take a moment to start-up, if the site has not been visited for the day. Yet this will be way more suitable than serving a website from a Raspberry Pi.

One of my main concerns is how to handle the database. Presently, the Raspberry Pi also hosts the database. In the future, this will likely also be hosted by Heroku. This will require us to shift away from sqlite3 and use either MySql or PosGres, which I have not used.

Furthermore, I am not sure how it will work to have the Raspberry Pi write to a database that is in the cloud. Clearly we will need to setup authentication. Once we do that, and tell the program where to find its database, presumably the information will find its way to the database. I have my doubts as to whether this will be as reliable as having script, server and database on the same machine. However, it seems obvious that this issue is one that is solved for any functional websites, so I am confident we will be able to execute a solution.

I will now try this solution and my next post will cover methods and results on the journey of relocating KegKong to his future cloudy habitat.