---
layout: post
title: Build A Portfolio With A Blog Using GitHub Pages
date: 2021-06-23
last_modified_at: 2021-06-23
cover_image: 2021-06-23-feature.png
author: Simon Dosda
categories: ruby jekyll
---

# 2 - Create Content

Our blog currently have 3 pages already. 

- index.markdown: the home page of our site
- about.markdown: an about page that can be accessed from the header
- _post/yyyy-mm-dd-welcome-to-jekyll.markdown: an example of a blog post

The beauty of Jekyll is that all page are written in markdown, which is readable and easy to use elsewhere. The navigation is also created automatically. 

For instance the index page is empty beside defining that it uses the home layout. Mostly the home layout is about displaying the list of the blog posts.

Any content that you add will be added on top of this list by default. You can also change the title for post with the property `list_title`. Finally, I put a `title: ''`  to avoid the page being displayed in the header.

```markdown
<!-- index.markdown -->
---
layout: home
list_title: Read Our Latest Posts
title: ''
---

# Github Pages Demo Blog

Welcome to this demo blog!

This website is intended to show you how to easily build and deploy a blog / portfolio with GitHub Pages and Jekyll.

You can find the sources of this project [here](https://github.com/SimonDosda/gp-blog).
```

## Add a static page

Let's add another static page to our project. If you see this website as your portfolio, something interesting to add would be a link to your public repositories.

To do so, create a new file `projects.markdown` in the root folder.

First think to fill up is the *Front Matter* part, the part in between dashes which indicate the metadata of the page.

In our case we want a page with a title and permalink of projects.

```yaml
---
layout: page
title: Projects
permalink: /projects/
---
```

GitHub Pages allows you to easily loop on your public repositories, as they are accessible from the variable `site.github.public_repositories`. *Jekyll* uses [liquid template](https://jekyllrb.com/docs/liquid/) to manage variables and statements. It allows us to loop over our repositories. 

I only display repositories that are not forked and contains topics, but feel free to use your own conditions.

I then display the repository name as a linked title, the description, the list of topics and the date it was last updated.

Here is the code and the result.

```markdown
{% for repo in site.github.public_repositories %}

{% if repo.fork == false and repo.topics.size > 0 %}

## [{{ repo.name }}]({{ repo.html_url }})

{{repo.description}}

Topics: {{ repo.topics | array_to_sentence_string}}

Last updated: {{repo.updated_at | date_to_string}}

{% endif %}

{% endfor %}
```

![Projects Page](/assets/images/2021-06-23-projects.png)

Notice that the *Projects* link was added automatically to the navigation bar.

## Create a blog post

Our blog already contains a welcome blog post create by *Jekyll*. Let's add another one to see the main functionalities you will probably use in your posts: titles, code and images.

As the welcome post states, to create a new post you need to create a new file with the template `yyyy-mm-dd-title.markdown`. 

Let's create a new blog post explaining furthermore how to write a blog post, so you both learn from this content and the one we are writing. Go ahead a create a file `2021-06-01-write-a-post.markdown` in the `_posts` folder.

```markdown
---
layout: post
title: "Write a Post"
date: 2021-05-31
categories: jekyll blogging
---

The goal of this article is to add some extra info about blog writing with _Jekyll_.

## Structure your posts

Use level 2 (`##`) and if necessary level 3 (`###`) titles to structure your posts.

## Display code snippets

You can display a block of code like the following using the 3 backticks before and after. You can specify the language after the 3 first backticks.

```python
def hello(name):
    return f'hello {name}'
```

## Add images

Create an `assets` folder where you can put all your images, then link them like this `![my inspiring image](/assets/sample-image.jpg }})`.

![my inspiring image]({{ "/assets/sample-image.jpg" | relative_url }})
_Photo by [Ian Schneider](https://unsplash.com/@goian)_
```

First things provided are the Front Matter metadata. Only `layout: post` and `title` are mandatory, but you can also add the date, categories, author, and so on. You can see more details [here](https://jekyllrb.com/docs/posts/).

We then have our article written in *markdown*, were I display a few extra tips, and below is the result.

![Blog Post](/assets/images/2021-06-23-post.png)