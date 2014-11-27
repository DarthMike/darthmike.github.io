---
layout: post
title: "Travis + octopress = Yay!"
date: 2014-11-27 22:45:25 +0000
comments: true
categories: [tips, continous integration, meta]
---

I just finished setting up [Travis CI](http://www.travis-ci.com) integration with this blog. So now I don't even need to do rake generate_deploy.
Just git push to source and deploy after 3 mins. Happiness.

I based my config on other's people awesome work: [Rogerz Zhang's post](http://rogerz.github.io/blog/2013/02/21/prose-io-github-travis-ci/) and [Hari Menon's post](http://www.harimenon.com/blog/2013/01/27/auto-deploying-to-my-octopress-blog/).

My small contribution to whoever happens to read this post is:
**Don't forget to set up your user.name and user.email** in Travis CI. Check my final [travis.yml](https://github.com/DarthMike/darthmike.github.io/blob/source/.travis.yml) if you are curious.

Happy coding!
