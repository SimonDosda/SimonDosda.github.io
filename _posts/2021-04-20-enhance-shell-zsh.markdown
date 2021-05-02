---
layout: post
title: An Enhanced Shell With ZSH
date: 2021-04-20
cover_image: 2021-04-20-feature.jpg
author: Simon Dosda
categories: shell tool
excerpt_separator: <!--more-->
---

When I started my first work as a developer, the rules were clear:

> We request our employees to work on Unix systems so they get used to our servers environment.
>
> <cite>My New Boss</cite>

Which meant I had to switch to Linux...

<!--more-->

Not that I never tried it before but every time I couldn't see a real benefit to it, while hardware recognition was often a problem and it lacked some software like the Adobe suite.

So I jumped on the Linux bandwagon, ... _and I loved it!_

When it comes to coding, Linux is amazing.
You have incredible scripting functionalities and an easy use of main developer tools like git, node, python, docker, ...

All of this is greatly due to the shell! While most of the humanity has totally forgotten about command line interfaces to the benefit of more accessible graphical user interfaces, the shell remains an incredible tool for developers seeking automation and efficiency.

As one of our main tools, it would be too bad not to shape it to our needs. In this article I will present my current setup. I am very interested to know yours though, do not hesitate to share it in the comment section.

## Meet the Z shell

While the default bash shell does a great job, there are several reasons to prefer its younger cousin, zsh.

Among those, it comes with a better auto-completion feature, combined with a spelling correction and approximate completion when a command is entered.

![zsh spelling correction](/assets/images/2021-04-20-autocomplete.png)
_zsh spelling correction in action_

It also provides interesting usage features such as [convenient search patterns using globbing](https://linuxaria.com/howto/globbing-con-zsh){:target="\_blank"} or the use of _vi_ when typing a command (you can enable it with `set -o vi`).

Last but not least, it provides a lot of customization of the prompt or functionalities with plugins, which I will come back to shortly.

## How to install it ?

Before starting to customize our shell, we need to set zsh as our default shell.

```bash
chsh -s /bin/zsh
```

For customization, the easiest way is to use a framework, the most well known being [Oh My ZSH](https://ohmyz.sh/). You can install it with a simple command.

```bash
curl -L https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh | sh
```

And... That's it!

Now let's dive into the customization of our z shell.

## Some useful plugins

[A lot of plugins](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins){:target="\_blank"} are already downloaded by Oh My Zsh, you just need to enable the one you want by adding them to the plugins list of your zshrc file.

Here are the plugins I use on my end:

- [git](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/git){:target="\_blank"}: provides a set of shell aliases for git (`g: git`, `gco: git checkout`, `gcb: git checkout -b`, ...).

- [extract](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/extract){:target="\_blank"}: allows you to extract a file by doing `extract file.tar.gz` (can be used with the option `-r` to remove the original file). Much easier to remember than `tar zxvf file.tar.gz`!

- [virtualenv](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/virtualenv){:target="\_blank"}: displays the current python virtual environment name in the prompt ([see below for its customization](#display))

- [zsh-autosuggestion](https://github.com/zsh-users/zsh-autosuggestions){:target="\_blank"}: the one I can't live without anymore! It displays auto completion based on the previous commands typed. I use it with an extra configuration, `bindkey '^ ' forward-word`, that allows you to accept a suggestion word for word by typing `ctrl + space` (while hitting the right arrow accepts the complete line).

![zsh-autosuggestion](/assets/images/2021-04-20-suggest.png)
_zsh autosuggestion, once you have tried it, there is no coming back_

## <a name="display"></a>A custom display

Oh My Zsh comes with a lot of [predefined themes](https://github.com/ohmyzsh/ohmyzsh/wiki/Themes) for your prompt, and you might just find the one you want there.

This is how I started, but I was quickly frustrated. Either I liked the colors of one but not the information displayed, or I liked the display but something was missing and the colors were not to my taste.

Trying several themes can be quite time consuming, so I decided to start from a theme I liked and customized it to get what suited me. In my case this is how it looks like:

![my custom display](/assets/images/2021-04-20-display.png)

It is pretty straight forward. It displays (when relevant) the name of your python environment (as python is one of my main programming language), then the name of the computer, the local directory in relative format and the current git branch (when relevant as well).

I like the simplicity and compactness of it: all the information that I need is here, on one line and with no loss of space. This is very personal and I strongly advise you to configure your own theme.

For this, there's nothing easier, just go to the theme file that you want to modify (in my case _~/.oh-my-zsh/themes/geoffgraside.zsh-theme_) and adapt it to your need. Here is what I came up with:

```bash
PROMPT='%{$fg[red]%}$(virtualenv_prompt_info)%{$reset_color%}% %{$fg[cyan]%}%n%{$reset_color%}:%{$fg[green]%}%~%{$reset_color%}$(git_prompt_info) %(!.#.$) '
ZSH_THEME_GIT_PROMPT_PREFIX=" %{$fg[yellow]%}("
ZSH_THEME_GIT_PROMPT_SUFFIX=")%{$reset_color%}"<
```

Each value is framed with `%{$fg[<color>]%}<value>{$reset_color%}` to set the foreground color. I use `$(virtualenv_prompt_info)` to display my virtual environment (thanks to the virtualenv plugin presented above), `%n` to display the computer name and `%~` for the relative path.

For the display of the git branch name, I use `$(git_prompt_info)` with a specific prefix and suffix that allows me to get rid of displaying `git:` before the branch name.

## Conclusion

I will stop here. There is much more to discover about zsh, and I hope I gave you a desire to take ownership of your shell.

The best thing I can advise you is to give it a try and then progressively add functionalities that suit you.
