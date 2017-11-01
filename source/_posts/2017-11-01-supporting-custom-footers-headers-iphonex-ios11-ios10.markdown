---
layout: post
title: "Supporting custom footers and headers for iPhoneX, iOS11 and iOS10"
date: 2017-11-01 21:00:45 +0000
comments: true
categories: ios, uikit
---

I've recently added support for custom footers and headers at [Peak] to work properly under iOS11 and iOS10, and look great for iPhoneX. With a bonus of reusing views as much as possible. During the process I found it not to be so simple and I want to share my approach here.

<!-- more -->

# Safe area

iOS 11 introduces the concept of the *Safe Area* of a view, and this is what you will be using to lay out content that respects sections of the view that can't be used for your content. Examples of area out of safe bounds is the status bar on 'classic' iPhones, the extended status bar and the bottom home indicator for iPhoneX:


<img src="/images/posts/iphonex-safearea.svg" width="300" height="600">

# Our goal

We want to have a reusable view or component that can be added to any view controller laying out content full screen. You might have buttons or any other content, and the key here is that we want to add some kind of decoration or background stretching from the safe area to the edge of the screen. This is exactly what the tab bar does in iOS system apps:

// TODO: Maybe edit image and highlight the bottom stretching
![contacts-iphone](/images/posts/iphonex-contacts.png)

Let's see how we can achieve this with our custom views.

# Approaches

## iOS 11 options: Use 'additionalSafeAreaInsets' or not

## iOS 10 compatibility

## Using safe area layout guide

# Final solution

# Conclusion

The new safe area concept is a very welcome addition to UIKit, and it's perfect to accomodate 'chrome' content and notify this to contained views or view controllers. We have seen that with a bit of design we can leverage the behaviour of iOS11 and the automatic conversion of constraints to iOS10, to have reusable components that look great on all devices with minimal customization across different iOS versions.

If you have any comments or thoughts I'd love to hear them, you can reach me on [twitter][twitter].

[twitter]: https://twitter.com/miguelquinon
[peak]: http://peak.net
