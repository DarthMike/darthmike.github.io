---
layout: post
title: "Controlling time in the app"
date: 2016-10-26 19:38:37 +0100
comments: true
categories: tips ios tools
---

Today I want to share a small utility we've been using for a while at [Peak][peak], my current workplace, to control the system time inside the application and save time while testing or debugging.

<!-- more -->

In most application code you'll eventually end up with tasks that need to execute periodically, or after some time has passed.  The period of time will depend on the actual application requirements, and might change from the order of seconds to days. For example, you might have a data cleanup every 30 days, your application might ping a backend for synchronization every 10 minutes, or by the start of every day, some data needs to be generated and presented to the user.

## Foundation and dates

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

For this code to be testable it's a very common practice to pass the `Date` in, as we do in the example above. This will improve testability as the date can be controlled from unit tests, thus isolating the system date from the date the component uses to operate.

But what about the times when you want to check the integration between the code and the system date? What if you want to trigger code that reacts to `UIApplicationSignificantTimeChange`?

The traditional way to test this kind of interactions is to change the date on the device, be it on iOS directly, or you or computer if running the simulator. But it would be very useful to control the system date and time inside the application without having to change the system date in the device or your computer.

## Time travel

To control the time from a debugging menu in the application, we used a library called [TUDelorean][tudelorean]. This library does some runtime method substitutions to trick any user of `Date` to a date that is no longer tied to the system, but controlled by us. This class is intended to used for unit tests, and that's how I've used it in the past. But you can also use it inside your app for testing purposes, and offset the time all the code, even system code, sees.

You can build a small in-app debugging menu where you can specify the amount of time to move from the system date, and trigger the update through the library while you run the app.

Shifting the time is very easy:
```swift
let futureDate: Date // Calculate your future date here

TUDelorean.timeTravel(to: futureDate)
```

After this, any code calling `Date` will be handled a fake date not tied to the system anymore. With this you can influence your components, and even trigger application events. For example, if you move your date a day forward, the system will emit `UIApplicationSignificantTimeChange` as if a real new day passed in the system clock. This is very useful because you'll be able to test integrations otherwise impossible from unit tests.

When you're done with testing, and want to return to your 'current' system date, just reset it with the provided method:
```swift
TUDelorean.backToThePresent()
```

## Conclusion

Using a tool designed for unit tests, you can build a small utility that can make your day to day development tasks more effective and less tedious. Here we looked at how controlling the time while running your application is useful, and we used a [library][tudelorean] designed for use in unit tests for our in-app debugging usage.

I hope you find this as useful as we've found it. Suffice to say that it should not be used for more than testing and debugging.

[peak]: http://www.peak.net
[tudelorean]: https://github.com/tuenti/TUDelorean
