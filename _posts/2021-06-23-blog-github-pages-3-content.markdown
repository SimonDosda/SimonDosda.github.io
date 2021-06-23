---
layout: post
title: Create Blog Posts And Static Pages With Jekyll and GitHub Pages
date: 2021-06-23
last_modified_at: 2021-06-23
cover_image: 2021-06-23-feature.png
author: Simon Dosda
categories: ruby jekyll
---

This article is part of a series showing you how to quickly and freely build and host your own [Jekyll](https://jekyllrb.com/) blog on [GitHub Pages](https://pages.github.com/). This series will also cover more advanced topics like adding a comment system directly in our code using [Staticman](https://staticman.net/) and adding privacy-friendly but still free analytics using [Umami](https://umami.is/).

I divided the tutorial into several parts:

- [Introduction]({% post_url 2021-06-21-blog-github-pages-1-foreword %})
- [Setting Up]({% post_url 2021-06-22-blog-github-pages-2-setup %})
- Create Content - you are here
- [Customize Display]({% post_url 2021-06-24-blog-github-pages-4-custom %})
- [Comment System]({% post_url 2021-06-25-blog-github-pages-5-comment %})
- [Analytics]({% post_url 2021-06-26-blog-github-pages-6-analytics %})

Now that we have initialized our project, let's see how to manage our content by: 
- updating our home page to add some information before the list of blog posts
- adding a static page showcasing our best GitHub projects
- adding a new post to our brand new blog

## Updating our home page

Our blog already has 3 pages:
- `index.markdown`: the home page of our site
- `about.markdown`: an about page that we can access from the header
- `_post/yyyy-mm-dd-welcome-to-jekyll.markdown`: an example of a blog post

The beauty of _Jekyll_ is that all pages are build using markdown, which is more readable than HTML and easy to reuse elsewhere. For instance, I often start writing my posts using [Notion](https://www.notion.so) and then export them in markdown. And a lot of blogging platforms, like [dev.to](https://dev.to) or [hashnode](https://hashnode.com/), use this language for their post editor.

_Jekyll_ also creates the navigation of the website automatically, whether you are adding a static page or a blog post. 

The list of blog posts is displayed in the home page, which corresponds to the `index.markdown` file. If you look at the file, you will see that this one is almost empty: all the logic behind the display of the blog list is indeed hidden in the home layout.

We can update the file to custom our home page.

For instance, we can change the title above the list of posts using the property `list_title`.

Moreover, we can add some welcome message on top of our page. We just need to add some content to the file, and it will be automatically placed above the post list. 

Finally, I also put a `title: ''` property as a trick to avoid _Jekyll_ using the first heading of my content as title and putting it in the header.

Here is the updated code of the file.


```markdown
<!-- index.markdown -->
---
layout: home
list_title: Read Our Latest Posts
title: ''
---

# Github Pages Demo Blog

Welcome to this demo blog!

This website intends to show you how to easily build and deploy a portfolio with a blog using _GitHub Pages_ and _Jekyll_.

You can find the sources of this project [here](https://github.com/SimonDosda/gp-blog).
```

## Adding a static page that showcases our best GitHub repositories

Let's add another static page to our project. If you see this website as your portfolio, something interesting to add is a showcase of your public GitHub repositories.

To do so, create a new file `projects.markdown` in the root folder.

The first thing to do is to fill up the *Front Matter* metadata (the part in between dashes).

In our case, we indicate for the page a title and permalink of `projects`.

```yaml
---
layout: page
title: Projects
permalink: /projects/
---
```

GitHub Pages allows you to easily loop on your public repositories, as they are accessible from the variable `site.github.public_repositories`. *Jekyll* uses [Liquid templating language](https://jekyllrb.com/docs/liquid/) to manage variables and statements. It allows us to loop over our repositories. 

To filter on my most interesting repositories, I only display repositories that are not forked and contain topics. Feel free to use the conditions that suit you the best.

I then display the repository name as a linked title, its description, list of topics and last update date.

Here is the code and the result.

{% raw %}
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
{% endraw %}

![Projects Page](/assets/images/2021-06-23-projects.png)

Notice that the *Projects* link was added automatically to the navigation bar.

## Adding a blog post

Our blog already contains a welcome blog post created by _Jekyll_. Let's add another one to see the main functionalities you will probably use in your posts: titles, code, and images.

As the welcome post states, to create a new post you need to create a new file with the template `yyyy-mm-dd-title.markdown`. 

Let's create a new blog post explaining furthermore how to write a blog post. Go ahead a create a file `2021-06-01-write-a-post.markdown` in the `_posts` folder.

{% raw %}
````markdown
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

You can display a block of code like the following using triple backticks. 
You can also specify the language after the first triple backticks.

```python
def hello(name):
    return f'hello {name}'
```

## Add images

Create an `assets` folder where you can put all your images, 
then display them with a link starting with an exclamative mark like this: 
`![my inspiring image]({{/assets/sample-image.jpg | relative_url }})`.

![my inspiring image]({{ "/assets/sample-image.jpg" | relative_url }})
_Photo by [Ian Schneider](https://unsplash.com/@goian)_
````
{% endraw %}

The first things provided are the Front Matter metadata. Only `layout: post` and `title` are mandatory, but you can also add the date, categories, author, and so on. You can see more details [here](https://jekyllrb.com/docs/posts/).

We then have our article written in *markdown*, where I display a few extra tips.

Below is the corresponding result.

![Blog Post](/assets/images/2021-06-23-post.png)