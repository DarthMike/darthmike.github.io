---
layout: post
title: "Swift Animations"
date: 2015-02-05 20:00:14 +0000
comments: true
categories: [swift, iOS, UI]
---
Today I introduce [a small Swift DSL](https://github.com/DarthMike/SwiftAnimations) to improve how complex and chained animation code looks, and reads.
<!-- more -->

#But… we already have a very good API!

We iOS Developers are spoiled. We have a rich set of Frameworks built by Apple, and some of the APIs are very idiomatic and easy to use. 

This is the case of UIKit animations. A simple method call and you can achieve *fire-and-forget* animations, all asynchronously executed and interpolated for you. But these APIs are part of the *’legacy’* of [Cocoa Touch](http://nshipster.com/the-death-of-cocoa). Sometimes you feel wanting a bit more, specially when writing Swift code. 

Let’s look at how the simplest kind of animation looks like in Code:
```swift
UIKit.animateWithDuration(0.2) {
	self.view.center.x += 100
}
```

**Amazing!** There is a lot going on here; Interpolating values for you, refreshing screen automatically and giving you proper callbacks at the end.

Unfortunately, in any iOS app, these kind of animations are rarely useful. Let’s see a more involved and tweaked animation. Imagine a photo taking app, with a button to save to disk the captured image. Now you want to do this animation:

- Snap to center
- Move to Save button
- Scale up a bit
- Scale till disappearing in the button

Code might look like this:
```swift
// Create snapshot
UIView.animateWithDuration(0.3, animations: {
  // Scale Snapshot
}, completion: {
  UIView.animateWithDuration(0.3, animations: {
    // Move to button
  }, completion: {
    UIView.animateWithDuration(0.3, animations: {
      // Animate scale up
    }, completion: {
      UIView.animateWithDuration(0.3, animations: {
        //Scale to 0
      }, completion: {
        // Cleanup
      })
    })
  })
})
```

#Nesting and chaining animations
##Dealing with Pyramids

The previous code works but feels *dirty and hacky* to me. How can we do better? How can we simplify it and make it more readable?

It seems *easy* to tweak. We actually have several options; The first one is to extract the animation blocks to inner functions in the calling method, like so:

```swift
let animationA = {
// Animation
}

let animationB = {
// Animation
}

let animationC = {
// Animation
}

let animationD = {
// Animation
}

UIView.animateWithDuration(0.3 , animations: animationA) {
  UIView.animateWithDuration(0.3, animations: animationB) {
    UIView.animateWithDuration(0.3, animations: animationC) {
      UIView.animateWithDuration(0.3, animations: animationD) {
        // Cleanup
      }
    }
  }
}
```
Some may argue this is ok, and I will argue it is better but not worse than the [Swift optional pyramids of Doom](http://www.scottlogic.com/blog/2014/12/08/swift-optional-pyramids-of-doom.html) or *if else if else if else* blocks.

Now bear in mind that this example **this is the simplest chain of animations** we can come up with. Now there are **variants** you might need for every animation. Say the second animation curve is different, durations are obviously different.

##Core Animation
Another way to approach this is to turn to Core Animation. That is what I actually did in a recent project. The problem is that Core Animation will be even more verbose than this, and actually you need to take into account the difference between presentation and model trees, and overall write more code.

Core Animation is awesome and powerful, but this comes at a price: verbosity and more complex API. I just want to chain a series of animations to do small but simple UI tweaks. The kind of small things that make iOS apps so special.

Core Animation API is more suited to do very advanced animations, follow paths when animating, when your animation is so complex that layers are more efficient, or when you need to do masking or 3D transforms.

##Keyframe animations
Another option to simplify the pyramids is to use keyframe animations. This is true, you can add arbitrary points and values for an animation, and it is very powerful. 

But in my opinion the UIKit keyframes API is flawed and makes you duplicate information and keep track of relative durations and values. Like:
```swift
        UIView.animateKeyframesWithDuration(1.0, delay: 0, options: UIViewKeyframeAnimationOptions.CalculationModeCubic, animations: {
            UIView.addKeyframeWithRelativeStartTime(0, relativeDuration: 0.2, animations: animationA)
            UIView.addKeyframeWithRelativeStartTime(0.2,relativeDuration: 0.3 animations: animationA)
            UIView.addKeyframeWithRelativeStartTime(0.3, relativeDuration: 0.5, animations: animationC)
        }, completion: {
            // Cleanup
        })
```
Not bad, but still not clean, and there is repeated information between end and start of the next animation. Also, it’s still very verbose.

##Prototyping
What about if you are prototyping the animation. You want a non-intrusive and simple API that lets you change things in a breeze. You may want to rewrite these animations or reorder them many times till you are satisfied with the result.

#A DSL approach
So I’ve been thinking it would be good to be able to express simply what we want do: **Animate** view A here, **then** rotate another view, **and then** fade out the previous view.

So my the same block of animations will look like this:
```swift
animate {
	//First animation
}.thenAnimate {
	//Second animation
}.thenAnimate {
	//Third 
}.withOptions(.CurveEaseOut).thenAnimate {
	//Fourth 
}.withOptions(.CurveEaseOut).thenAnimate {
	//
}.start()
```

I think it’s a big improvement. I believe only the most complex animation code should look *dirty and hacky*. Most UI code does not need to look like it is written in a rush. Specially if we think that we need to actually run it to see the movements. 

I might change the API a bit in the following days, but the idea stays the same.

The code is on [Github](https://github.com/DarthMike/SwiftAnimations). Take a look at it and [tell me what you think](http://twitter.com/miguelquinon).


