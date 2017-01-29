---
layout: post
title: "Build settings depending on Xcode version"
date: 2017-01-29 23:26:54 +0000
comments: true
categories: swift xcode tips
---

This weekend I wanted to setup [Precompiled Bridging Headers][headers] for my project. This setting is available in the new Xcode 8.3 beta.  Then building for latest official XCode (8.2.1) the code will not compile because the Swift compiler doesn't recognize it. How do we use different build settings for different Xcode versions? **Only possible with xcconfig files**.

There's an undocumented build setting called `XCODE_VERSION_MINOR` that we can use here. And after some variable substitution we'll make it work. Here's the gist:

```bash
// Setting for last Xcode
OTHER_SWIFT_FLAGS_XCODE_0820 = //<previous flags, if any>
// Setting for Xcode Beta
OTHER_SWIFT_FLAGS_XCODE_0830 = -enable-bridging-pch
// Switch configuration based on Xcode version
OTHER_SWIFT_FLAGS = $(OTHER_SWIFT_FLAGS_XCODE_$(XCODE_VERSION_MINOR))

```

Full credit to Samantha Marshall's [excellent writeups of xcconfig files][xcconfig]. Take a read, very useful to refer to.

[headers]: https://swift.org/blog/bridging-pch/
[xcconfig]: https://pewpewthespells.com/blog/migrating_code_signing.html#working-in-both-worlds
