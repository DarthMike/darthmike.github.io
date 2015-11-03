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

I always like to start with the repository itself; Looking at the history, commits and overall activity. Many times you will find that developers are not used to providing consistent and meaningful commit messages (how many times you’ve seem **WIP feature X** commit message?). So looking at the history is not informative. So I prefer to look at global statistics instead.

Assuming you will work with a git repository, the first tool I like to use is [git stats][git_stats]. As easy as:

```bash
gem install git_stats
cd <repo>
git_stats generate
```

Running this tool will result in a nice html document giving insights about authors, commits, files and lines of code. It’s mostly informative but it’s good to get an overview of the key collaborators of a project, who created, and most importantly, who deleted the most lines of code!





############
git
git bisect
show tree
sloccount
https://github.com/PaulTaykalo/objc-dependency-visualizer
https://code.google.com/p/gource/
analizer
warnings
basic stuff:
copy vs strong array/dict/string
weak refs and blocs
#########


[peak]: http://peak.net
[git_stats]: https://github.com/tomgi/git_stats