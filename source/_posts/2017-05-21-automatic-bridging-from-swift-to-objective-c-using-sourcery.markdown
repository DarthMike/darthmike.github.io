---
layout: post
title: "Automatic bridging from Swift to Objective-C using Sourcery"
date: 2017-05-21 22:30:00 +0100
comments: true
categories: tools meta-programming swift Objective-C
---

I recently read a [great post][Bridging-post] by [Benjamin Encz][encz] about bridging Swift to Objective-C by creating bridging types that can be exposed to the older language. I wondered if his idea can be improved and automated by use of meta-programming with [Sourcery][Sourcery] by [Krzystof Zablocki][zablocki].

<!-- more -->


1. Fast review of Sourcery
2. Fast review of the approach
3. Template
4. Does it work? caveats


[Bridging-post]: http://blog.benjamin-encz.de/post/bridging-swift-types-to-objective-c/
[Sourcery]: https://github.com/krzysztofzablocki/Sourcery
[encz]: https://twitter.com/benjaminencz
[zablocki]: https://twitter.com/merowing_
