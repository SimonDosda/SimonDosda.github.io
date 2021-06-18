---
layout: post
title: Build A Portfolio With A Blog Using GitHub Pages
date: 2021-06-22
cover_image: 2021-06-22-feature.png
author: Simon Dosda
categories: ruby jekyll
---
# 1 - Setting Up

## Create Your GitHub Pages Repository

To deploy your website using GitHub Pages, you need to create a new public repository using the following convention for the name: *<username>.github.io*.

![GitHub Pages Repository](/assets/images/2021-06-22-repo.png)

Once you are done, the content of your repository will be deployed on *https://<username>.github.io*. You can try adding a *index.html* file with "Hello World" or anything you like in it to check everything works as expected.

You can also deploy a website from any repository, which is very usefull to deploy documentation of your projects for instance. In this case the url will be *https://<username>.github.io/<repository-name>.*

For this tutorial I will use a specific repository, called [gp-blog](https://github.com/SimonDosda/gp-blog) for Github Pages Blog, but you most probably want to deploy your blog/portfolio at the root folder.

## Create Our Jekyll Static Website

Jekyll is a Ruby Gem, so make sure you have all the prerequisites installed to be able to use it. You can find their list and the procedure to install them on your os at [https://jekyllrb.com/docs/installation/](https://jekyllrb.com/docs/installation/).

Once this is done, install *Jekyll* and *Bundler* gems.

```bash
gem install jekyll bundler 
```

We can now initialize our jekyll project in the repository we just created with the following command.

```bash
jekyll new .
```

Once this is done, open the *Gemfile* file and follow the instruction to deploy on GitHub Pages, by commenting the `gem "jekyll"` line and uncommenting the `gem "github-pages"` one.

```bash
# This will help ensure the proper Jekyll version is running.
# Happy Jekylling!
# gem "jekyll", "~> 4.2.0"
# If you want to use GitHub Pages, remove the "gem "jekyll"" above and
# uncomment the line below. To upgrade, run `bundle update github-pages`.
gem "github-pages", "~> 214", group: :jekyll_plugins
```

For the version (here 214), you can find the latest version [here](https://pages.github.com/versions/).

You can then type the command  `bundle update` to install your dependencies and serve your website locally with the command `bundle exec jekyll serve --livereload`. 

Your new blog is now served on [localhost:4000](http://localhost:4000). As you can see, it needs some setup.

## Edit The Global Configuration Of Our Blog

We can now start customizing our new blog. The first thing to do is to update the `_config.yml` file. There you can edit your blog title, its description, your email address and github username. 

You can also enter your twitter username or comment it if you don't have any.

You can also add the property `show_excerpts: true` to display posts' excerpts on the home page, and change the permalink generation for pages with the `permalink` attribute. I like to use a less nested structure like the following.

```yaml
permalink: /:collection/:year-:month-:day-:title:output_ext
```

Even though we use the `--livereload` option, changes on the `_config.yml` file will not be taken into account. You need to kill your server and relaunch it, and you should see your changes appear in your browser.

If everything is ok, you can commit your changes and push them. 

Note that you can edit the branch from which you website is deployed on your GitHub repository in *Settings → Pages →Source*.

Here we are, we now have our basic blog up and running! 

## Add a Plugin to Open External Link on a New Tab

one of the standard but annoying behavior of the blog we just created is that when you click on a link, it will go directly to it, losing your reader. The hack to avoid that in html is to set a `target=_blank` property to your anchor, which translate to something like this in markdown.

```ruby
[link](url){:target="_blank"}
```

In top of not being very elegant and easy to forget, this is very annoying because it won't work when you import your article in another source, for instance in *dev.to*.

To avoid that we can install the plugin [Jekyll Target Blank]() which forces all external links to open in a new tab. It is also a good way to see how we can add plugins to our website.

First you will need to add the gem to your `Gemfile` by adding the following line.

```ruby
group :jekyll_plugins do
  ...
  gem 'jekyll-target-blank' # Forces all external links to open in a new browser window
end
```

Then add it to the list of plugins in your `_config.yml` file.

```yaml
plugins:
  ...
  - jekyll-target-blank
```