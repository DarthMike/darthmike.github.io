---
layout: post
title: "Xcode 7.3 analyzer improvements"
date: 2016-01-21 20:39:53 +0000
comments: true
categories: tools ios xcode
---

The [latest xcode beta][xcode] is available for downloading. There are many improvements, and the usual problems with stuff that stops working... Ah the love of Apple for it's developers :). But I want to tell you about a feature that caught my attention: **New analyzer settings!**

# New analyzer settings

From the [release notes][xcode-release-notes]:

> The static analyzer warns when nil is returned from a method or function with a nonnull return
type or is passed to a parameter marked nonnull.

> The static analyzer checks for common misuses of Objective-C generics.

> The static analyzer checks for missing localizability. This check is off by default and can be
enabled by selecting ‘Yes’ for ‘Missing localizability’ in the 'Static Analyzer - Generic Issues’ build
settings.

There are 3 new settings related to these new features: CLANG_ANALYZER_NONNULL, CLANG_ANALYZER_OBJC_GENERICS  and CLANG_ANALYZER_LOCALIZABILITY.

# CLANG_ANALYZER_NONNULL and CLANG_ANALYZER_OBJC_GENERICS

Objective-C continues to get some love! Adding into the changes for [nullability][nullability] and [generices][generics], now the analyzer will start to warn you about usage not in line with your annotations. That's very good news, considering that the compiler checks were a bit lacking. Additional kudos because both settings are enabled by default.

# CLANG_ANALYZER_LOCALIZABILITY

This is an intriguing one. Let's investigate further; Let's say you have some code where you set a string to a label:

```swift
label.text = @"not-localized";
```
You'll now get a nice analyzer warning: "User-facing text should use localized string macro"

![warning1](/images/posts/user-facing-warning.png)

But you won't get a warning when the key is not localized. Like:
```swift
label.text = NSLocalizedString(@"not-localized", nil);
```
Hey, we can't have everything, but here's my [request for improvement submitted][radar].

Anyway, go ahead and enable these new settings! And use the analyzer!

[xcode]: https://developer.apple.com/xcode/download/
[xcode-release-notes]: http://adcdownload.apple.com/Developer_Tools/Xcode_7.3_beta/Xcode_7.3_beta_Release_Notes.pdf
[nullability]: {{site.url}}/blog/2015/04/17/adopting-nullability-annotations/
[generics]: {{site.url}}/blog/2015/06/09/adopting-objectivec-generics/
[radar]: http://openradar.appspot.com/radar?id=4993758374395904
