---
layout: post
title: "Automatic bridging from Swift to Objective-C (II)"
date: 2017-05-28 19:02:31 +0100
comments: true
categories: tools meta-programming swift Objective-C
---

In the [previous post][part1] I've been playing with the idea of generating bridging classes for our Swift-only. I want to elaborate on two things that maybe were not clear by reading my first post. Firstly, I'll complete the templates so we can generate not only bridging for our types, but also for the receving API boundary in the Swift code. Secondly, I'd like to elaborate a bit more on when (and if) to apply this to real world projects.

[The repository][repo] has been updated with all the changes if you want to check it.

<!-- more -->

## TLDR

- You can generate methods on your receiving Swift code, modelled as an extension. These methods are the ones you can use to call from Objective-C
- It's better not to overuse this. If you have a small project, I would say you're better off rewriting in Swift. If you have a big project (as in [Benjamin's example][original-post]), using this you will avoid writing boilerplate. **But** remember it's just a temporary solution up until code is migrated to Swift.


## Bridging the boundary to Swift

The Objective-C code will eventually call some other code in Swift. This is the **boundary** between Swift and Objective-C. The whole purpose of the [previous post][part1] was describing how we could bridge the Swift-only types that the methods to call accept or return.

If you would like to use this for real, then the Objective-C code is still not able to call into Swift, as the [compiler-generated header file][header], doesn't expose methods that contain Swift-only types. You'll need to manually write those, or again, generate that code.

Let's see an example, taken from my [repository][repo]:
```swift
@objc(XYZSwiftReceiverAPI)
final class SwiftReceiverAPI : NSObject {
    func sendStruct(_ value: StructWithSwiftProperties) {
        print(value)
    }

    func sendOtherEnum(_ value: OtherEnum) {
        print(value)
    }
}
```

These methods can't be used from Objective-C because they use Swift-only types; an enum and a struct in this case. We need to generate methods that mirror the types but substitute the ones that are bridgeable. These methods just forward to the original API on the object.
```swift
extension SwiftReceiverAPI {
    func sendStruct(_ value: _ObjCStructWithSwiftProperties) {
        sendStruct(value.structWithSwiftProperties)
    }

    func sendOtherEnum(_ value: _ObjCOtherEnum) {
        sendOtherEnum(value.otherEnum)
    }
}
```

### Handling methods without return type

Let's go through what we need to do in order to build a template to generate the simple case of methods with multiple arguments but no return type.



### Handling methods with return types

Here we'll cut the amount of work by just supporting types that we bridge with our previous template (types marked with `AutoObjCBridgeable`). We won't handle tuples, functions returning these or any other complex situations. Read the last part of this post for the reasons behind this decision.



## Is this useful at all?


[part1]: {{site.url}}/blog/2017/05/21/automatic-bridging-from-swift-to-objective-c-using-sourcery/
[original-post]: http://blog.benjamin-encz.de/post/bridging-swift-types-to-objective-c/
[header]: https://developer.apple.com/library/content/documentation/Swift/Conceptual/BuildingCocoaApps/MixandMatch.html
[repo]: https://github.com/DarthMike/AutoObjCBridgeable
