---
layout: post
title: "Xcode 8: New build settings and analyzer improvements"
date: 2016-07-26 11:34:16 +0100
comments: true
categories: tools ios xcode
---

I always like to check the new build settings and analyzer improvements of every XCode release. And this year's main release includes a couple of goodies. Let's check them out!


# Analyzer improvements

From the current Xcode 8 beta 3 [release notes][notes]:

> The static analyzer check for nullability violations supports both aggressive and less aggressive levels
of checking. The more aggressive level checks for nullability violations in all calls

This is a very nice addition for anybody dealing with *legacy* Objective-C code and audited APIs. So remember to enable it in your project as it won't be enabled after your upgrade.

**TODO: Setting name and example**

> The clang static analyzer now checks for improper cleanup of synthesized instance variables in
-dealloc methods

Most possibly a minor one, but remember that the analyzer can give you false positives in legacy code.

**TODO: Example**

# New build settings

> “Active Compilation Conditions” is a new build setting for passing conditional compilation flags to
the Swift compiler.

> Two new build settings have been added to enable Swift compiler options: -suppress-warnings
(SWIFT_SUPPRESS_WARNINGS) and -warnings-as-errors (SWIFT_TREAT_WARNINGS_AS_ERRORS).

**TODO: Elaborate with examples**

# EMBEDDED_CONTENT_CONTAINS_SWIFT is deprecated

Basically this setting has been deprecated. See what it was doing here in the [Apple technote][technote1]. You might encounter problems when building your application so it's worth upgrading to the new setting if you are using it.

> The new build setting ALWAYS_EMBED_SWIFT_STANDARD_LIBRARIES replaces the use of
EMBEDDED_CONTENT_CONTAINS_SWIFT. This setting indicates that Xcode should always embed
Swift standard libraries in a target for which it has been set, whether or not the target contains Swift
code.

I'm guessing these are changes preparing ahead of a binary compatible Swift 4 release. But otherwise I don't see what the new build setting does differently.

# XCConfig files

If you like to use [xcconfig files][xcconfig] then this new release has some small improvements as well. I'm very happy!

> .xcconfig files support conditional inclusion of other .xcconfig files, using the syntax #include?

What does this mean?

**TODO**

And the last one:

> Xcode takes build settings set in xcconfig files into account when suggesting updates to your build
settings.

[notes]: http://adcdownload.apple.com/Developer_Tools/Xcode_8_beta_3/Release_Notes_for_Xcode_8_beta_3.pdf
[xcconfig]: https://pewpewthespells.com/blog/xcconfig_guide.html
[technote1]: https://developer.apple.com/library/ios/qa/qa1881/_index.html
