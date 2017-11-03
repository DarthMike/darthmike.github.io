---
layout: post
title: "Supporting custom footers for iPhoneX, iOS11 and iOS10"
date: 2017-11-03 21:00:45 +0000
comments: true
categories: ios, uikit
---

I've recently added support for custom footers and headers at [Peak] to work properly under iOS11 and iOS10, and look great for iPhoneX. With a bonus of reusing views as much as possible. During the process I found it wasn't so simple so I want to share my approach here.

<!-- more -->

# Safe area

iOS 11 introduces the concept of the *Safe Area* of a view, and this is what you will be using to lay out content that respects sections of the view that can't be used for your content. Examples of area out of safe bounds is the status bar on 'classic' iPhones, the extended status bar and the bottom home indicator for iPhoneX:

<img src="/images/posts/iphonex-safearea.svg" width="300" height="600">

# Our goal

We want to have a reusable view or component that can be added to any view controller laying out content full screen. You might have buttons or any other content in that custom footer. We also want to add some kind of decoration or background stretching from the safe area to the edge of the screen. This is exactly what the tab bar does in iOS system apps:

// TODO: Maybe edit image and highlight the bottom stretching
![contacts-iphone](/images/posts/iphonex-contacts.png)

Notice that even though the actual content of the tab bar (the icons) is inside the safe area, **the decoration** (bar background) extends to the edge of the view. This is something that maybe you will want to do if you have custom controls. For scrolling content is specially important, otherwise your footer will 'float' over the content and you'll see your content below the home button. For example for Peak Brain Training we have a button at the bottom of the screen, and decoration gradient that gradually masks the content underneath.

If you have views that don't use a navigation bar, you will want to respect the safe area, so this concept also applies to headers on the top of the screen as well.

Let's see how we can achieve this with our custom views across OS versions trying to reuse code as much as possible.

# Approaches

I will be assuming you use Auto layout, because it's simpler and the modern way to lay out interfaces under iOS. Using manual layout with safe areas is supported but I won't cover it here.

## iOS 11 options

### Use 'additionalSafeAreaInsets' or not

Along the new APIs related to safe area, there's a new property called `additionalSafeAreaInsets` on `UIViewController`. Setting it on the view controller will allow you to specify the height of the footer (relative to the safe area). It will automatically adjust `safeAreaInsets` in it's view and subviews accordingly, and also modify the `contentInset` of a contained `UIScrollView`.

This property is great for generic container view controllers, like a custom UITabbarController, that you would build yourself. For our case, building a general footer that we can **compose** into our view controllers, it has the tradeoff of only making it available for iOS 11. We would need to handle the iOS 10 case specially.

### Safe area layout guide

# Final solution

# Conclusion

The new safe area concept is a very welcome addition to UIKit, and it's perfect to accomodate 'chrome' content and notify this to contained views or view controllers. We have seen that with a bit of design we can leverage the behaviour of iOS11 and the automatic conversion of constraints to iOS10, to have reusable components that look great on all devices with minimal customization across different iOS versions.

If you have any comments or thoughts I'd love to hear them, you can reach me on [twitter][twitter].

[twitter]: https://twitter.com/miguelquinon
[peak]: http://peak.net
