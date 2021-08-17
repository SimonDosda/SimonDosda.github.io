---
layout: post
title: Build A Portfolio With A Blog Using GitHub Pages
date: 2021-06-26
last_modified_at: 2021-06-23
cover_image: 2021-06-26-feature.png
author: Simon Dosda
categories: ruby jekyll
---

This article is part of a series showing you how to quickly and freely build and host your own [Jekyll](https://jekyllrb.com/) blog on [GitHub Pages](https://pages.github.com/). This series will also cover more advanced topics like adding a comment system directly in our code using [Staticman](https://staticman.net/) and adding privacy-friendly but still free analytics using [Umami](https://umami.is/).

I divided the tutorial into several parts:

- [Introduction]({% post_url 2021-06-21-blog-github-pages-1-foreword %})
- [Setting Up]({% post_url 2021-06-22-blog-github-pages-2-setup %})
- [Create Content]({% post_url 2021-06-23-blog-github-pages-3-content %})
- [Customize Display]({% post_url 2021-06-24-blog-github-pages-4-custom %})
- [Commenting System]({% post_url 2021-06-25-blog-github-pages-5-comment %})
- [Analytics] - you are here

[umami](https://umami.is/)

Now that our blog is fully functional, our main job is to write interesting articles. 

If you are writing a blog you probably want people to read it, so you will also need to attract people on it.
You will therefore need to be able to follow your traffic, know which article attract the most people, on so on.

When it comes to gathering traffic data from your website, you mainly have two choice of products.

The first one is to use free solution. 
Let's be honest, we are talking about [Google Analytics](https://analytics.google.com/analytics/web/) here, which is already pre-integrated with Jekyll.
It is indeed very easy to setup, but raise some confidentiality issues as its trackers are very invasive, and all the data collected eventually goes to google.

If you are concerned about collecting your user data through a company that will use them, you can go for a more privacy focused company, like [Gauges](https://get.gaug.es/) or [Simple Analytics](https://simpleanalytics.com/). 
These companies will also allow you to collect visitors data from your website, but without exploiting them. 
Most of the time, they are less privacy invading and only collect relevant information. 
For instance, they usualy don't use cookies, allowing you to avoid having to put an annoying cookie banner for European visitors to comply to GDPR policies.

The counterpart for using a solution not exploiting your visitor's data? 
Well, companies need to make profit, so you need to pay for it. 
Which is totally ok and understandable, but you might not want to spend money just to know how many visitors come to your blog, which is already a generous donation of your time to help other.

Fortunately, development is an amazing world and some analytics company have open sourced their code. 
Here, we will see how we can deploy one of them, [Umami](https://umami.is/), and how to use it for our website.  

## Deploying Umami on Heroku

As for our comment system, we will deploy Umami on Heroku. You can see how to do so [here](https://umami.is/docs/running-on-heroku), but we will do it together anyway.

First you need to fork the [Umamy repository](https://github.com/mikecao/umami).

Then create a new app, and under the `Deploy` section choose `connect to Github` as `Deployment method`, search for the repository you just forked and connect to it.

![Connect to Github](/assets/images/2021-06-26-connect-github.png)

We then need to add a database. Go to the `Ressources` tab, search for `Heroku Postgres` in the `Add-ons` section and install it.

![Add Heroku Postgres](/assets/images/2021-06-26-heroku-postgres.png)

Now we need to create the required tables. Click on your new add-on (open in a new tab), then go to `Settings -> View Credentials`. 
You will get everything you need to connect to your database, in my case I have installed the [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli) so I just run the corresponding line in my terminal. 

![Connection to Postgres](/assets/images/2021-06-26-postgres-connection.png)

I then copied the lines from the [schema.postgresql.sql](https://github.com/SimonDosda/umami/blob/master/sql/schema.postgresql.sql) file to init the tables. 
You can check everything went ok by running the `\dt` command.

```sql
\dt

List of relations
 Schema |   Name   | Type  |     Owner      
--------+----------+-------+----------------
 public | account  | table | ivcqpajmowwzfs
 public | event    | table | ivcqpajmowwzfs
 public | pageview | table | ivcqpajmowwzfs
 public | session  | table | ivcqpajmowwzfs
 public | website  | table | ivcqpajmowwzfs
(5 rows)
```

Then go back to the app page and, in the `Settings -> Config Vars`, add an `HASH_SALT` environment variable with any random string as a key. 

![App Environment Variables](/assets/images/2021-06-26-congig-vars.png)

Phew, you can now go to the `Deploy` section and click `Deploy Branch` in the `Manual deploy` part.

![Deploy App](/assets/images/2021-06-26-deploy.png)

Once the build is finish, you will be able to open the app and log into it with the username `admin` and the password `umami`.

![Umami Login](/assets/images/2021-06-26-umami-login.png)

The first thing you want to do is to change this password in `Settings -> Profile -> Change password`.

We now have to add our website. To do so go to `Settings -> Website -> Add website`

![Add Website](/assets/images/2021-06-26-add-website.png)

Click then on `get tracking code`  to get the script line to add to your head.

![5%20-%20Add%20analytics%2025da5db67f994149b08aa7b655ea5c85/umami-traking-code.gif](/assets/images/2021-06-26-umami-traking-code.gif)

Redeploy your website with it and visit it, then go to your Umami dashboard, you should see your first visit! 

![Analytics Display](/assets/images/2021-06-26-analytics.png)

Yeah, my first visitor, myself!