---
layout: post
title: Add A Comment System To A Jekyll Blog Hosted On GitHub Pages Using Staticman
date: 2021-06-25
last_modified_at: 2021-06-23
cover_image: 2021-06-25-feature.png
author: Simon Dosda
categories: ruby jekyll
---

This article is part of a series showing you how to quickly and freely build and host your own [Jekyll](https://jekyllrb.com/) blog on [GitHub Pages](https://pages.github.com/). This series will also cover more advanced topics like adding a comment system directly in our code using [Staticman](https://staticman.net/) and adding privacy-friendly but still free analytics using [Umami](https://umami.is/).

I divided the tutorial into several parts:

- [Introduction]({% post_url 2021-06-21-blog-github-pages-1-foreword %})
- [Setting Up]({% post_url 2021-06-22-blog-github-pages-2-setup %})
- [Create Content]({% post_url 2021-06-23-blog-github-pages-3-content %})
- [Customize Display]({% post_url 2021-06-24-blog-github-pages-4-custom %})
- [Commenting System] - you are here
- [Analytics]({% post_url 2021-06-26-blog-github-pages-6-analytics %})

Now that we have our custom website with its blog, let's see how to add some interactivity with a commenting system.

Adding a commenting system to a Jekyll website is tricky as it is a static site generator, with no backend allowing us to store comments in a database and serve them.

The immediate solution to solve this issue is to use an external service with its own database. Among the popular ones, [disqus](https://disqus.com/) is often used with Jekyll. It has the advantage of being easy to implement, as it is a script loaded on your page, but it also presents privacy issues and displays ads in its free version.

Another possible solution to add dynamic content to a GitHub website is to use [staticman](https://staticman.net/). On the opposite of the previous solutions using external databases, _staticman_ creates files in your repository, updating your website statically. It is free and open-source but not as straightforward as _disqus_ to implement as you need to deploy an API for it. The nice thing is that it will store all your comments in your git repository, so there is no risk of losing them.

As we are courageous, this is the solution we will implement for our website.

## Deploying the app on Heroku

We will deploy a staticman instance on [Heroku](https://www.heroku.com). You might need to create an account first if you don't have one.

Hence this is done, go the the [staticman GitHub repository](https://github.com/eduardoboucas/staticman) and fork it.

On your Heroku dashboard, go to `New -> Create new app`, Type the name that you want for your app and hit `Create app`.

In the `Deployment method` of the `Deploy` tab, click on `GitHub`. Once connected, choose the `staticman` repository and hit `Connect`.

![Staticman Screen](/assets/images/2021-06-25-deploy-github.png)

Once this is done, you can go to the `Manual deploy` section and click on `Deploy Branch`.

This will deploy an application to [`https://<app-name>.herokuapp.com/`](https://sdosda-gp-blog.herokuapp.com/).
There should be an error diplayed. It is normal, we havent configure it yet. We will take care of it now.

The app will need to be able to access your github repository. To do so, create a new application in GitHub _Settings → Developer settings →GitHub Apps_.

Create an app with the following inputs:

- Homepage URL: `https://staticman.net/`
- Webhook URL: `https://<app-name>.herokuapp.com/v1/webhook`
- Repository permissions / Contents: `Access: read and write`
- Repository permissions / Pull Requests: `Access: read and write`
- Subscribe to events: `Pull request`

Once your app is created, go to `General → Private keys` and hit `Generate a private key`.

Go back to Heroku. Now it is time to configure our app.

In _Settings -> Config Vars_, create the following variables:

- GITHUB_APP_ID: the GitHub app id displayed at the beginning of the \_General\* section
- GITHUB_PRIVATE_KEY: the private key we just generated
- RSA_PRIVATE_KEY: a private key that will be used to encrypt content, you can use the same as GITHUB_PRIVATE_KEY

Go back to the deploy tab and run a manual deployment again.

Once the deployment is over, you should now see a welcome message when you visit your app.

## Adding Staticman to our project

Then create a `staticman.yml` file as follow.

```yaml
comments:
  # (*) REQUIRED
  #
  # Names of the fields the form is allowed to submit. If a field that is
  # not here is part of the request, an error will be thrown.
  allowedFields: ["name", "message"]

  # (*) REQUIRED
  #
  # Name of the branch being used. Must match the one sent in the URL of the
  # request.
  branch: "main"

  # Text to use as the commit message or pull request title. Accepts placeholders.
  commitMessage: "Comment from {fields.name} on {options.slug}"

  # (*) REQUIRED
  #
  # Destination path (filename) for the data files. Accepts placeholders.
  filename: "entry{@timestamp}"

  # The format of the generated data files. Accepted values are "json", "yaml"
  # or "frontmatter"
  format: "yaml"

  # List of fields to be populated automatically by Staticman and included in
  # the data file. Keys are the name of the field. The value can be an object
  # with a `type` property, which configures the generated field, or any value
  # to be used directly (e.g. a string, number or array)
  generatedFields:
    date:
      type: date
      options:
        format: "timestamp-seconds"

  # Whether entries need to be appproved before they are published to the main
  # branch. If set to `true`, a pull request will be created for your approval.
  # Otherwise, entries will be published to the main branch automatically.
  moderation: false

  # When allowedOrigins is defined, only requests sent from one of the listed domains will be accepted.
  allowedOrigins: ["localhost", "simondosda.github.io"]

  # (*) REQUIRED
  #
  # Destination path (directory) for the data files. Accepts placeholders.
  path: "_data/comments/{options.slug}"

  # Names of required fields. If any of these isn't in the request or is empty,
  # an error will be thrown.
  requiredFields: ["name", "message"]
```

You might have to change the name of the `branch` from which you are deploying, and definitely have to update the `allowedOrigins` value with your own domain, or remove it if you don't fell like you need this security.

In this post we will focus on building a simple comment system where any user can enter a `name` and a `message` in markdown. We will allow them to respond to other messages as well.

_Staticman_ allows you to check for spam automatically, to send email notification and to implement recapchta, but we wont cover this here.

In the `_config` file, add the following entry, with your own `app name`, github `username`, `repo` and `branch`.

```yaml
staticman_url: https://<app-name>/v3/entry/github/<username>/<repo>/<branch>/comments
```

## Creating the input form

In the `_includes` folder, add a new `comment_form.html` file.

{% raw %}

```html
<!-- _includes/comment_form.html -->
<form method="POST" action="{{ site.staticman_url }}" class="comment-form">
  <input
    name="options[redirect]"
    type="hidden"
    value="{{ page.url | absolute_url }}"
  />
  <input name="options[slug]" type="hidden" value="{{ page.slug }}" />

  <textarea
    class="comment-message"
    name="fields[message]"
    placeholder="Comment (markdown accepted)"
    required
  ></textarea>
  <div class="comment-bottom">
    <input
      class="comment-name"
      name="fields[name]"
      type="text"
      placeholder="Name"
      required
    />
    <button class="comment-submit" type="submit">SEND</button>
  </div>
</form>
```

{% endraw %}

The way staticman works it that we will send data from a form to our `staticman_url` we just defined.

As we defined in our `staticman.yml` config file, our comments are based on 2 fields:

- `name`: the name of the sender, defined by `<input name="fields[name]" required>`
- `message`: the content of the comment, define by `<textarea name="fields[message]" required></textarea>`

We also provide 2 options when we validate the form, defined by hidden inputs:

- `redirect`: the url to be redirect once the submit call will complete. Here we come back to our page
- `slug`: the slug of the page that will be used to create the comment folder, as we defined our `path` as `"_data/comments/{options.slug}"` in the `staticman.yml` configuration file

## Displaying the list of comments

Comments that will be sent to our repository will be stored in the `_data/comments/<slug>` directory. The way to access them in _Jekyll_ is with the variable `site.data.comments[page.slug]`.

Let's create a `comment_list.html` file in our `_includes` folder to display them.

{% raw %}

```html
<!-- _includes/comment_list.html -->
{% assign comments = site.data.comments[page.slug] | where_exp: "item", "true"
%} {% assign sorted_comments = comments | sort: 'date' %} {% for comment in
sorted_comments %}
<div class="comment">
  <h3>{{comment.name}}</h3>
  <time
    class="post-meta dt-published"
    datetime="{{ page.date | date_to_xmlschema }}"
    itemprop="datePublished"
  >
    {%- assign date_format = site.minima.date_format | default: "%b %-d, %Y" -%}
    {{ comment.date | date:"%H:%M - %b %-d, %Y, %Y" }}
  </time>
  <p>{{comment.message | strip_html | markdownify }}</p>
</div>
{% endfor %}
```

{% endraw %}

What we do here is looping over the page comments sorted by date. The first line with the `where_expression` filter is required to get the comment values.

For each comment we display the `name`, `date` and `message` with the `strip_html` filter to secure the message and`markdownify` filter to render markdown.

## Adding the comments block

We now have everything we need for our basic comments feature.

Let's add another file in our `_include` folder named `comments.html` that will wrap together our two comments snippets: posting a new comment and displaying receipt comments.

{% raw %}

```html
<!-- _includes/comments.html -->
<section class="comments">
  {% if site.data.comments[page.slug] %}
  <div>
    <h2>Comments</h2>
    {% include comment_list.html %}
  </div>
  {% endif %}

  <div>
    <h2>Leave a Comment</h2>
    {% include comment_form.html %}
  </div>
</section>
```

{% endraw %}

Now we can update our `_layout/post.html` file to display these comments instead of the `disqus` comments.

{% raw %}

```html
<!-- _layout/post.html -->
<div class="post-content e-content" itemprop="articleBody">{{ content }}</div>

{% include comments.html %}

<a class="u-url" href="{{ page.url | relative_url }}" hidden></a>
```

{% endraw %}

## Style our comments

Before pushing our brand new feature, let's add some style to our comments.

As it is a separate feature the best option is probably to create it's own css file. Let's create a new `comments.scss` in the sass file and add the import line `@import "comments";` in `assets/main.scss`.

```scss
// _sass/comments.scss
.comment {
  padding-top: 10px;
  h3 {
    display: inline;
    color: royalblue;
  }
  p {
    margin: 0;
  }
}

.comment-new {
  margin-top: 25px;
}

.comment-form {
  display: flex;
  flex-direction: column;
  margin-top: 25px;

  .comment-message,
  .comment-name {
    font-size: 16px;
    padding: 15px;
    border: 1px solid #ddd;
    margin: 0;
  }

  .comment-message {
    min-height: 150px;
    resize: none;
  }

  .comment-bottom {
    display: flex;
  }

  .comment-name {
    flex: 1;
  }

  .comment-submit {
    width: 200px;
    border: 1px solid #ddd;
    color: royalblue;
    font-weight: bold;

    &:hover {
      cursor: pointer;
    }
  }
}
```

You can now go ahead and deploy our comment system. When you send a comment, it should open a merge request, that will be automatically validated it you set `moderation: false` in the staticman configuration file.

![List of comments](/assets/images/2021-06-25-comment-list.png)

Our system comment is now functional!

But there is still some improvements that can be added, starting by allowing to respond to a comment.

## Add reply feature

To enable replying to a message, the first thing we need to add is a `parent_id` field in our messages. To Allow this you need to modify the `staticman.yml` file as follow.

```yaml
# staticman.yml
comments:
  allowedFields: ["name", "message", "parent_id"]
  ...
```

The `requiredFields` list stay the same as the first level messages won't have a parent message.

Then we need to add this field in our `comment_form.html` file.

{% raw %}

```html
<!-- _includes/comment_form.html -->
<form method="POST" action="{{ site.staticman_url }}" class="comment-form">
  <!-- options inputs -->

  <input
    name="fields[parent_id]"
    type="hidden"
    value="{{ include.parent_id }}"
  />

  <!-- user fields inputs -->
</form>
```

{% endraw %}

This field is store in an hidden input field like this and uses the `parent_id` value given by the include.

Now we need to add this form below each comment to be able to reply, with a corresponding parent id. For each comment we will also display the list of comments with its `parent_id`, which are the replies to this comment. To do so we will recursively include `comment_list.html`.

{% raw %}

```html
<!-- _includes/comment_list.html -->
{% assign parent_id = include.parent_id | default: '' %} {% assign comments =
site.data.comments[page.slug] | where_exp: "item", "item.parent_id == parent_id"
%} {% assign sorted_comments = comments | sort: 'date' %} {% for comment in
sorted_comments %}
<div class="comment">
  <h3>{{comment.name}}</h3>
  <time
    class="post-meta dt-published"
    datetime="{{ page.date | date_to_xmlschema }}"
    itemprop="datePublished"
  >
    {%- assign date_format = site.minima.date_format | default: "%b %-d, %Y" -%}
    {{ comment.date | date:"%H:%M - %b %-d, %Y, %Y" }}
  </time>
  <p>{{comment.message | strip_html | markdownify }}</p>
  <div class="comment-reply">
    <p>Reply to {{ comment.name }}:</p>
    {% include comment_form.html parent_id=comment._id %} {% include
    comment_list.html parent_id=comment._id %}
  </div>
</div>
{% endfor %}
```

{% endraw %}

This is now what we have (don't forget to add the property `parent_id` to your current messages) if we send a reply. I also added `.comment-reply { padding: 15px; }` in `_sass/comments.scss`.

![Comment Reply Feature](/assets/images/2021-06-25-comment-reply-unfinished.png)

It's a start. Be we definitely don't want to display all these reply boxes, but only see them if we hit a reply button. This change can be done quite easily with only some html and css.

First let's update our comment reply div.

{% raw %}

```html
<!-- _includes/comment_list.html -->
...
<div class="comment-reply">
  <input id="reply-{{ comment._id}}" type="checkbox" class="checkbox" />
  <label class="open" for="reply-{{ comment._id }}"
    >Reply to {{ comment.name }}</label
  >
  <label class="close" for="reply-{{ comment._id }}">X</label>
  {% include comment_form.html parent_id=comment._id %} {% include
  comment_list.html parent_id=comment._id %}
</div>
...
```

{% endraw %}

What we do here is that we had a checkbox that will not be displayed but instead targeted by the two labels, `reply to ...` to open the reply box and `X` to close it. Each label will be display depending on the state of the checkbox.

Here is the css code to make it work.

```scss
// _sass/comments.scss
.comment-reply {
  padding: 15px;
  padding-top: 5px;
  display: flex;
  flex-direction: column;

  .open {
    font-style: italic;
  }

  .close {
    display: none;
    align-self: flex-end;
    padding: 5px 10px;
    border: 1px solid #ddd;
  }

  .open:hover,
  .close:hover {
    cursor: pointer;
  }

  .comment-form {
    display: none;
  }

  .checkbox {
    display: none;
    &:checked ~ .open {
      display: none;
    }
    &:checked ~ .close {
      display: block;
    }
    &:checked ~ .comment-form {
      display: flex;
    }
  }
}
```

We now have our reply feature functioning!

![Comment Reply Animation](/assets/images/2021-06-25-comment-list.gif)

## Use a markdown editor for messages

There is still a few improvements we can make to our new functionality. The first one is to use a markdown editor to allow user to easily edit their comments in markdown.

To do so we will use a javascript markdown editor called [SimpleMDE](). This is quite an elegant solution as this library will target our textarea and replace them, which mean that our solution will sill work if one of our user has javascript disabled on its browser (who does that?).

First we need to had the link to the library and its css in our `head` file.

{% raw %}

```html
<!-- _includes/head.html -->
<head>
  ...

  <!-- Simple Markdown Editor -->
  <link
    rel="stylesheet"
    href="https://cdn.jsdelivr.net/simplemde/latest/simplemde.min.css"
  />
  <script src="https://cdn.jsdelivr.net/simplemde/latest/simplemde.min.js"></script>
</head>
```

Then edit the comment form html to add a `SimpleMDE` instance attached to each `textarea`.

```html
<form method="POST" action="{{ site.staticman_url }}" class="comment-form">
  ...
  <textarea
    id="message-{{ include.parent_id }}"
    class="comment-message"
    name="fields[message]"
    placeholder="Comment (markdown accepted)"
    required
  ></textarea>
  ...
</form>

<script>
  var simplemde = new SimpleMDE({
    element: document.getElementById("message-{{ include.parent_id }}"),
    forceSync: true,
    spellChecker: false,
    status: false,
    placeholder: "Comment (markdown supported)",
  });
</script>
```

{% endraw %}

I also added some change to its style.

```scss
// _sass/comments.scss
.CodeMirror,
.editor-toolbar {
  border-radius: 0;
}
.CodeMirror,
.CodeMirror-scroll {
  min-height: 150px;
}
```

And here we are!

## Better management of redirect

Currently when we send a comment, we are redirected to the page we are on. This behavior is quite confusing, generally we expect to get a notification that our comment was sent. Also, as our website did have the time to build with the comment we just send, everything look as if nothing happened!

Let's work on improving this part. While using a javascript library would allow use to send our comment and refresh the comment list asynchronously, this is not possible with our solution that is fully static. It is probably one of the main drawback.

What we can do to improve the experience a bit is to go to a thank you page displaying that the comment will be readable soon.

To do so we have to create a new page, `comment-success.markdown`, that will be almost the same as `index.markdown`.

```markdown
## <!-- comment-success.markdown -->

layout: home
list_title: Read Our Latest Posts
title: ''

---

## Thank you!

Your comment was successfully sent!

It will appear on our website soon.
```

Now change the redirect option in `_includes/comment-form.html` to `<input name="options[redirect]" type="hidden" value="{{ page.url | absolute_url }}">`.

And that's it!

Of course there is a lot that could still be done to improve our comment system, but that's already quite a start. And we fully build it ourselves!
