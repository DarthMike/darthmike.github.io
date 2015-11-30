---
layout: post
title: "Transitioning iOS versions"
date: 2015-11-28 20:06:25 +0000
comments: true
categories: iOS tips
---

Having worked with companies developing their own products, I've faced many times the problem developers encounter when building applications: Your biggest 3rd party dependency, that is the OS SDK, is changing under you every year. How do you deal with this in a scalable way? 

<!-- more -->

# Long-term projects

Unless you do consulting or freelancing, chances are that you will have to deal repeatedly with the code in the same project over the years. Even if you jump from project to project, chances are you land into existing projects where there's some code written some time ago.

It's in longer-term projects that [software rot][rot] happens faster. A successful application will face this earlier than expected, and in the fast-paced market of Mobile applications, every year Apple is renovating and ruthlessly changing the underlying Operating System where our code lives. It requires non-trivial amount of work to keep up with changes, while still producing code for our users. 

I've been in teams where those questions were often raised:
- How do we keep modernising our code, while maintaining backwards compatibility for our users? 
- What techniques  can be use to keep moving fast?
- How do we throw away old code using deprecated APIs in a big codebase?

# Fragmentation

In my opinion, developers in the Apple ecosystem face a different kind of fragmentation. I've [written previously][fragmentation] about it. 

A mobile engineering team should embrace change, knowing they'll need to assign some engineering resources to keeping up with code changes, otherwise the code will rot faster than expected. (Anybody heard about complete rewrite of a feature just written a year ago?).

I'm going to share with you what I consider are best practices to keep up with OS upgrades in an iOS project.

# Best practices to transition

I divide these best practices in 3 categories:

- Language: Swift / Objective-C
- SDK use
- Maintenance of previous releases

Let's see them one by one.

## Swift

This is the first and most important one. 

## Objective-C

- Modernisation
- New language features (- Generics and nullability)
- Code templates up to date

## SDK use

- Deprecated use? warning or error?
- class clusters wrapping temporary API
- Marking legacy versions (MQG_IOSX) so you remove code later
- Check for iOS version, not class/method existence


## Maintenance of previous releases

- CI with beta/current/1 version before
- Cocoapods in repository


# Conclusion


[rot]: https://en.wikipedia.org/wiki/Software_rot
[fragmentation]: {{site.url}}/blog/2015/05/27/fragmentation/