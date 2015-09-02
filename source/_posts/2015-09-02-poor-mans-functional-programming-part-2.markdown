---
layout: post
title: "Poor man's functional programming: FP in the small"
date: 2015-09-01 10:56:59 +0100
comments: true
categories: ["swift", "functional programming"]
---

In a [previous post][part1], I wrote about the choice of paradigms that Swift presents for it's target audience. I also gave my opinion on how people with different backgrounds might see and approach the Functional Programming paradigm.

In this post I want to share how I started to take advantage of FP for the practical domain of iOS developers: Building apps.

<!-- more -->

# Silver bullets

In software development there are no silver bullets to kill your problems and move on. We always need to find tradeoffs and evaluate our options to decide what would be the best choice. We often need to consider not only the technical aspects but cultural or team aspects too.

iOS applications involve programming to eventually show and move *things* on screen. The iOS frameworks are object-oriented, and Swift is not a pure FP language. Throwing away all the code from Apple's frameworks just to make our code more functional is not a realistic approach.

Thus we need to balance where we want to use different programming paradigms. For a beginner, or a team with not too much experience with FP, the pragmatic option is doing [Functional Programming in the small, Object Oriented in the large][small_large]. We'll focus on how to start with the former.

# FP in the small using Swift

## Basics
I want to start by reviewing what we can learn to use Swift more effectively, focusing on functions.

### Functions everywhere!
- Learn to read signature
- Swift syntax helps
- Methods as first-class functions
- Closures, computed variables

### Must-have functions
- Map
- Filter
- Reduce
- flatMap

### Operators
See ThoughtBot/Runes
See ThoughtBot/Argo
- bind/flatMap
- apply

## Practical use

### Data transformations
0. Bye bye loops
1. JSON parsing
2. Image transformations
3. Deriving values from source of truth

### Networking
1. Completion blocks and futures
2. Chaining complex operations

### Structuring the files differently
1. Reusable functions
2. Scopes of work

## Swift features to move away from OOP

- Inmutability
- Generics
- Protocols and protocol extensions

## Conclusion

[part1]: {{site.url}}/blog/2015/08/31/poor-mans-functional-programming/
[small_large]: http://www.johndcook.com/blog/2009/03/23/functional-in-the-small-oo-in-the-large/