---
layout: post
title: "Controlling time in the app"
date: 2016-10-26 19:38:37 +0100
comments: true
categories: tips ios tools
---

Today I want to share a small utility we've been using for a while at [Peak][peak], my current workplace, to control the time inside the application and save time while testing or debugging.

<!-- more -->

In most application code you'll eventually end up with code that needs to execute periodically, or after some time has passed. The periods of time will depend on the actual feature of the application, and might change from the order of seconds to days. For example, you might have a data cleanup every 30 days, your application might ping a backend for synchronization every 10 minutes, or by the start of every day, some data needs to be generated and presented to the user.

## iOS and NSDate

You'll eventually use `NSDate` and `NSCalendar` (or `Date` and `Calendar` in Swift) to calculate when your tasks should be executed. As an example, say you have a task scheduler, and it will run tasks after a specific amount of time passed:

```swift
protocol Task {
  func execute()
}

class Scheduler {
  func schedule(_ task: Task, every: TimeInterval) {
    // Schedule and save task to run every x seconds
  }

  func run(with date: Date) {
    // Actually perform the calculations and fire due tasks
  }
}
```

For this code to be testable it's a very common practice to pass the `Date` in, as we do in the example above. This will improve testability as the date can be controlled from unit tests, thus isolating the system date from the date of the actual component.

But what about the times when you want to check the integration between the code and the system date? It would be very useful to control the system date and time inside the application without having to change the system date in the device or your computer.

## Time travel

To control the time from a debugging menu in the application, we used a library called [TUDelorean][tudelorean]. This library does some nasty hacks to trick any user of `Date` to a date that is no longer tied to the system, but controlled by us. This class is intended to used for unit tests, and that's how I've used it before. But you can also use it inside your app for testing purposes, and shift the time all the code sees.

If you need to control the time from within the application, you'll just need to call the library:
```swift
let futureDate: Date // Calculate your future date here

TUDelorean.timeTravel(to: futureDate)
```

And when you're done with testing, and want to return to your 'current' system date, just reset it with the provided method:
```swift
TUDelorean.backToThePresent()
```

## Conclusion

Using a tool designed for unit tests, you can build a small utility that can make your day to day development tasks more effective and less tedious.

I hope you find this as useful as we've found it. Suffice to say that it should not be used for more than testing and debugging.

[peak]: http://www.peak.net
[tudelorean]: https://github.com/tuenti/TUDelorean
