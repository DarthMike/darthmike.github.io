---
layout: post
title: "Jumping into existing codebases"
date: 2015-11-03 22:15:18 +0000
comments: true
categories: iOS git tools productivity
---

I’ve recently changed workplace, ([we’re hiring!][peak]), and I’ve jumped into an existing codebase. This is a good time to review what I like to do when I jump into unknown codebases.

<!-- more -->

# Examining the repository

## Git stats
I always like to start with the repository itself; Looking at the history, commits and overall activity. Many times you will find that developers are not used to providing consistent and meaningful commit messages (how many times you’ve seem **WIP feature X** commit message?). So looking at the history is not informative. So I prefer to look at global statistics instead.

Assuming you will work with a git repository, the first tool I like to use is [git stats][git_stats]. As easy as:

```bash
gem install git_stats
cd <repo>
git_stats generate
```

Running this tool will result in a nice html document giving insights about authors, commits, files and lines of code. It’s mostly informative but it’s good to get an overview of the key collaborators of a project, who created, and most importantly, who deleted the most lines of code!

### Gource

A very interesting way to see the commit history in a graphical way is [gource][gource]. I always like to run it for 5 minutes just to get a feel of the rush of the project, and basically to see
in a glance what are the core parts of the codebase. It also is very funny to look at, and you can get a feel what is the core part of the repository, what parts changed the most, and the number of developers working at the same time. It's available via [homebrew][homebrew] as well so very easy to install and run:

```bash
brew install gource
cd <repo>
gource
```

Check the available options, like `seconds-per-day`, to customize how you see the animation.

## Commit messages

I like to just do a `git log` in terminal and understand if the team has any kind of agreement on what and how commit messages are structured. Having good commit messages beats any amount of commented code kept around and is a very integral part of the project.

There has been plenty of discussion on the subject, I’m just going to link here 2 of my favorite articles: [this][commit_message] and [this one][commit_message2]. I don’t adhere to all the points presented but agree to many of them. You’ll get an overview of how a project was evolved by looking at some logs, specially from the beginning. Just do:

```bash
git log --reverse
```

Having good commit messages helps a lot when dealing with bug fixes, using [git bisect][git_bisect]. When you're the new one in a project you don't have the domain and code knowledge to understand what changed or debug, you can save hours of pain for  bug fixes by using it, and when you nail down the commit that broke something, you can understand the motivation behind it much better.



## Structure

## Dependency management

# Code

sloccount
sloccount
https://github.com/PaulTaykalo/objc-dependency-visualizer

############
analizer
warnings
basic stuff:
copy vs strong array/dict/string
weak refs and blocs
#########


[peak]: http://peak.net
[git_stats]: https://github.com/tomgi/git_stats
[commit_message]: http://chris.beams.io/posts/git-commit/
[commit_message2]: https://robots.thoughtbot.com/5-useful-tips-for-a-better-commit-message
[git_bisect]: https://git-scm.com/docs/git-bisect
[gource]: http://gource.io
[homebrew]: http://brew.sh
