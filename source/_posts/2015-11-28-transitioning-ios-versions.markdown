---
layout: post
title: "Transitioning iOS versions"
date: 2015-11-28 20:06:25 +0000
comments: true
categories: iOS tips
---

Having worked in companies developing their own products, I've faced many times the problem developers encounter when building iOS applications: Your biggest 3rd party dependency - the OS SDK - is changing under you every year. How does your team deal with this in a maintainable way?

<!-- more -->

# Long-term projects

Unless you do consulting or freelancing, chances are that you will have to deal repeatedly with evolving code in the same project over the years. Even if you jump from project to project, chances are you land into existing projects where there's some code written some time ago.

It's  longer-term projects that [software rot][rot] happens faster. A successful application will face this earlier than expected, and in the fast-paced market of Mobile applications, every year Apple is renovating and ruthlessly changing the  Operating System where our code lives. It requires non-trivial amount of work to keep up with changes, while still producing code for our users. 

I've worked in teams where those questions were often raised:

- How do we keep modernising our code, while maintaining backwards compatibility for our users? 
- What techniques  can be use to keep moving fast?
- How do we throw away old code using deprecated APIs in a big codebase?

# Fragmentation

[As I wrote before][fragmentation], developers in the Apple ecosystem face a different kind of fragmentation. We're forced to adopt breaking changes every year, or 'die' in the process. Apple forces us to start using new features of the OS, and has a policy of deprecating APIs a lot faster than we like.

A mobile engineering team should embrace change, knowing they'll need to assign some engineering resources to keeping up with code changes, otherwise the code will rot faster than expected. (Anybody heard about complete rewrite of a feature just written a year ago?).

I'm going to share with you what I consider are best practices to keep up with OS upgrades in an iOS project.

# Best practices to transition

I would divide best practices in 3 categories:

- SDK use
- Language: Swift / Objective-C
- Maintenance of previous releases

Let's review them one by one.

## SDK use

- Deprecated APIs: I think we should always treat warnings as errors, and it goes true with deprecated APIs use. If this is not managed around the release of the new OS version, chances are it will take time for the team to keep up with new API. It's best to modernise code to use newer API in the face of deprecations, than leaving warnings as *TODO* markers for later. 

- Check for iOS version: For long time, Apple's recommended way of checking for API existence has been using `respondsToSelector` or `[SomeClass class]`. Apple introduced availability checks for Swift this year, and I think we should change our practices and check for OS version in Objective-C. There's several pitfalls related to runtime checks, and so many variations depending on what you are checking for, that I don't see any benefit at all to check for methods, classes etc. Apple has acknowledged the flaws of the previous practices and moved forward with Swift. We should to the same for Objective-C code.

- Mark legacy code paths with a specific marker	

- class clusters wrapping temporary API


- 3rd party libs

## Objective-C

Chances are your project still has lots of Objective-C code. And it's this code that needs special care, as the team will need to continue living with it, even in the face of a newer programming language. Focus on:

- Modernisation: Specially Swift interoperability features; [nullability][nullability] and [generics][generics]. This will ease using older code from Swift, without the need for a rewrite.
- Rewrites in Swift: I think the future of Cocoa development is Swift, and any application-level Objective-C code is destined to die sooner rather than later.
- Keep code templates up to date: Your team should have up to date code templates, specially dealing with nullability annotations added by default. Developers generally forget to add them.

## Swift

This is the first and most important one. 





## Maintenance of previous releases

- CI with beta/current/1 version before
- Cocoapods in repository


# Conclusion


[rot]: https://en.wikipedia.org/wiki/Software_rot
[fragmentation]: {{site.url}}/blog/2015/05/27/fragmentation/
[nullability]: {{site.url}}/blog/2015/05/27/fragmentation/
[generics]: {{site.url}}/blog/2015/06/09/adopting-objectivec-generics/