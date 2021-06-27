---
layout: post
title: Build A Portfolio With A Blog Using GitHub Pages
date: 2021-06-24
last_modified_at: 2021-06-23
cover_image: 2021-06-24-feature.png
author: Simon Dosda
categories: ruby jekyll
---

# 3 - Customize Display

The thing I like about *Jekyll* is that is provide you a very clean directory, most things are hidden to us and it just fells like magic. The drawback of this is that when you want to customize your theme, you need go to the theme repository to understand how to update it. 

## How the content is structured

So before doing anything, we need to have a look at our [minima theme](https://github.com/jekyll/minima). Please make sure you look at the branch from you current version, in my case it is still the 2.5 that is installed (you can have this info in the `Gemfile` file), so I am looking at the [2.5-stable branch](https://github.com/jekyll/minima/tree/2.5-stable).

Our blog structure mainly rely on 3 folders:

- `_layouts`: this is where the layout that we define in the Font Matter metadata of each markdown file.
- `_includes`: snippets of html code that are used in layouts.

For instance if we look at the post layout code, we see the following (shortened).

```markdown
---
layout: default
---
<article class="post h-entry" itemscope itemtype="http://schema.org/BlogPosting">
  <header class="post-header">
    <h1 class="post-title p-name" itemprop="name headline">
      {{ page.title | escape }}
    </h1>
    ...
  </header>

  <div class="post-content e-content" itemprop="articleBody">
    {{ content }}
  </div>
  ...
</article>
```

The blog layout use the default layout, it means that its code will be injected in the `content` variable. When we use this layout for a blog post, its content is injected in the `content` variable of the blog layout. Moreover, we see that we use the  `page` variable to display the title, date, author and so on.

Now if we look at the default layout, we see this.

```ruby
<!DOCTYPE html>
<html lang="{{ page.lang | default: site.lang | default: "en" }}">

  {%- include head.html -%}

  <body>

    {%- include header.html -%}

    <main class="page-content" aria-label="Content">
      <div class="wrapper">
        {{ content }}
      </div>
    </main>

    {%- include footer.html -%}

  </body>

</html>

```

The default layout represents the base structure of all our pages. Beside the `content` variable where the other layout are injected, it uses several `include` to inject html code. Let's dive into them and start customizing our blog by adding a favicon.

## Add a favicon

Let's start gently by adding a favicon to our website. If you are familiar with html, you know that the favicon is defined in the `head` tag, here defined in the `_includes/head.html` file.

The way *Jekyll* works is that you can override any file by putting it in your own project. In this case we can create our own `_includes/head.html`, copy the code from github and modify it to add our favicon.

For my favicon I went for [a lion](https://favicon.io/emoji-favicons/lion) for the sake of the exercise.

```ruby
# _includes/head.html
<head>
  <meta charset="utf-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  {%- seo -%}
  <link rel="stylesheet" href="{{ "/assets/main.css" | relative_url }}">
  {%- feed_meta -%}
  {%- if jekyll.environment == 'production' and site.google_analytics -%}
    {%- include google-analytics.html -%}
  {%- endif -%}
    
  <link rel="apple-touch-icon" sizes="180x180" href="{{ "/apple-touch-icon.png" | relative_url }}">
  <link rel="icon" type="image/png" sizes="32x32" href="{{ "/favicon-32x32.png" | relative_url }}">
  <link rel="icon" type="image/png" sizes="16x16" href="{{ "/favicon-16x16.png" | relative_url }}">
</head>
```

## Modify the header

Now you can override any file to customize your website as you want.  

Let's add our new logo to the header for instance, by modifying `_includes/header.html`.

```scss
<header class="site-header" role="banner">
    <div class="wrapper">
      {%- assign default_paths = site.pages | map: "path" -%}
      {%- assign page_paths = site.header_pages | default: default_paths -%}
      <a class="site-title" rel="author" href="{{ "/" | relative_url }}">
        <img src="{{ "favicon-32x32.png" | relative_url }}" />
        {{ site.title | escape }}
      </a>
      ...
    </div>
  </header>
```

## Customize your css

Their is several way to modify our css. As you can see in the `head.html` file, css is currently pulled from `assets/main.css`. In the development file it is actually a scss file, that import from the `_sass` directory.

The first way to customize your css is to copy/paste the `_sass` directory and edit its files as we did for the head file.

An other possibility is to generate use a new file that will be loaded at the end, so that rules we put here will take precedence over the default ones. This is the solution I will show you.

Let's first create the `assets/main.scss` file with the following code.

```ruby
# assets/main.scss
---
---

@import "minima";
@import "custom";
```

I just added an import of the file `custom`. We can now create this file in the `_sass` folder and create add some css.

```scss
.site-title {
    color: orangered;
    &:visited {
        color: orangered;
    }
}
```

Here I am just modifying the site title color to match our lion.

## Add a feature image to our posts

Something you will see in almost every blog is a feature image that is displayed in the blog list and at the top of an article. Unfortunately this is not taken into account be Jekyll now, so let's implement this feature ourselves.

This time we want to modify the blog layout directly, we can create our own version in `_layouts/post.html`.

What we are going to do is to check for a `feature_image` variable, and if it exists we will display it on top of our title by adding the following snippet.

```scss
{% if page.feature_image %}
  <div class="feature-image">
    <img src="{{ "/assets/" | append: page.feature_image | relative_url }}" />
  </div>
{% endif %}
```

Let's add a feature image to our last post. Put the image you want in your assets folder and add its name in the post metadata.

```yaml
---
layout: post
title: "Write a Post"
date: 2021-05-31
categories: jekyll blogging
feature_image: feature-image.jpg
---
```

You can then add some css to our `custom.scss` file to style it.

```scss
// _sass/custom.scss
...
.feature-image {
    margin-bottom: 50px;

    img {
        width: 100%;
        max-height: 250px;
        object-fit: cover;
    }
}
```

Here is the result.

![Post With Image](/assets/images/post.png)

## Update the home

Let's improve our home page.

First copy the `home.html` in the `_layout` folder. Following the same principle than for the post layout, we can add our feature images.

```ruby
# _layout/home.html
---
layout: default
---

<div class="home">
  {%- if page.title -%}
    <h1 class="page-heading">{{ page.title }}</h1>
  {%- endif -%}

  {{ content }}

  {%- if site.posts.size > 0 -%}
    <h2 class="post-list-heading">{{ page.list_title | default: "Posts" }}</h2>
    <ul class="post-list">
      {%- for post in site.posts -%}
      <li>
        <div>
          {%- assign date_format = site.minima.date_format | default: "%b %-d, %Y" -%}
          <span class="post-meta">{{ post.date | date: date_format }}</span>
          <h3>
            <a class="post-link" href="{{ post.url | relative_url }}">
              {{ post.title | escape }}
            </a>
          </h3>
          {%- if site.show_excerpts -%}
            {{ post.excerpt }}
          {%- endif -%}
        </div>
        {% if post.feature_image %}
          <div class="feature-image">
            <img src="{{ "/assets/" | append: post.feature_image | relative_url }}" />
          </div>
        {% endif %}
      </li>
      {%- endfor -%}
    </ul>

    <p class="rss-subscribe">subscribe <a href="{{ "/feed.xml" | relative_url }}">via RSS</a></p>
  {%- endif -%}

</div>
```

And update our `custom.scss` file.

```css
// _sass/custom.scss
...

.post-list > li {
    display: flex;
    flex-wrap: wrap-reverse;
    
    div:first-child {
        flex: 4 0 200px;
    }
    .feature-image {
        flex: 1 0 200px;
        margin-bottom: 0;
    }
}
```

And here is the result.

![Responsive Posts Image](/assets/images/home.gif)