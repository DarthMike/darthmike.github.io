---
layout: post
title: "Functional programming in the small, with Swift"
date: 2015-09-09 16:00:23 +0100
comments: true
categories: ["swift", "functional programming"]
---

In a [previous post][part1], I wrote about the choice of paradigms that Swift presents for it's target audience. I also gave my opinion on how people with different backgrounds might see and approach the Functional Programming paradigm.

In this post I want to share how I started to take advantage of FP with Swift for the practical domain of iOS developers: Building apps.

<!-- more -->

# Silver bullets

In software development there are no silver bullets to kill your problems and move on. We always need to find tradeoffs and evaluate our options to decide what would be the best choice. We often need to consider not only the technical aspects but cultural or team aspects too.

iOS applications involve a great deal of **UI** programming. The iOS frameworks are object-oriented, and Swift is not a pure FP language. Throwing away all the code from Apple's frameworks just to make our code more functional is not a realistic approach.

Thus we need to balance where we want to use from different programming paradigms. For a beginner, or a team with not too much experience with FP, my choice would be doing [Functional Programming in the small, Object Oriented in the large][small_large].

# FP in the small using Swift

Let's look at the features of Swift and the standard library that help write more 'functional' without having to restructure or change how we build a project.

## Better functions

One of the first terms you will hear when diving into FP is that functions are **first-class** citizens, or the language supports **higher-order** functions. 

What this means is that functions are treated like any other type. Think as functions being treated like objects; They can accept other functions as input, return other functions, and be stored and passed around.

Under this definition Objective-C *kind of* supports this, but it's lacking in several areas. Firstly, there's a difference between blocks and selectors, the way to use them is different and the syntax is more complex. Secondly, in Swift a closure is a function without name, and they both have the same type, whereas in Objective-C closures can only be expressed with blocks.

A simple example for iOS developers is:

```Swift
func animation() {
    view.transform = CGAffineTransformMakeScale(0.5, 0.5)
}

UIView.animateWithDuration(0.4, animations: animation)
```

Or with a closure:
```Swift
let animation {
    view.transform = CGAffineTransformMakeScale(0.5, 0.5)
}

UIView.animateWithDuration(0.4, animations: animation)
```

You couldn't do the first example with Objective-C selectors, specially considering that you can declare a function locally inside another function. The bigger difference for me is that the syntax is very concise and easy to understand. The language also has special handling for trailing closures which is very handy to keep the code readable. A side effect of this is that implementing DSLs using closures is very easy (check my library [SwiftAnimations][swiftanimations] or Robert Böhnke's [Cartography][cartography]). This might seem a minor point, but in my opinion readability of a programming language is very important.

## Functions (and closures) everywhere

Functions are everywhere, even disguised sometimes:

- Reference and value type methods are functions that take an implicit 'self' first argument
- Computed properties are closures
- Lazy variables are defined with a closure

Given that functions are everywhere my first recommendation is that you should learn to read and understand the signature. This is specially important when generics are involved, which is a very fundamental part of the language. 

A function signature conveys two crucial bits of information:

- The **shape** it has, so it can be used somewhere else where another function expects same signature
- **What** the function does. In some cases, specially in the standard library, reading the name and understanding the signature of a function, you can infer what it does. I don't think it replaces documentation but it's a big helper when reading code.

For example take the definition of map for optionals in Swift 2.0 standard library (T is the generic type of the optional itself):

```swift
/// If `self == nil`, returns `nil`.  Otherwise, returns `f(self!)`.
    func map<U>(f: @noescape (T) -> U) -> U?
```

Just looking at the declaration, specifically focusing on it's signature, we could say that mapping the optional returns an optional of different type, based on the function we pass in, that transforms between the two types.

Even though **Swift compiler does not guarantee functions are pure**, so we could be accessing the network, reading disk or returning nil for every input value, we can tell a lot from a function signature.

## Elementary functions

Given that FP is about functions, many programming languages offer similar 'basic' basic ones which are the building blocks of more complex logic. Understanding and knowing how to use these can make you start applying a more functional style across all your code easily.

> **Swift 2.0**: Many of the free functions in the standard library have been moved to methods in types or generic protocols. This is more in line with Apple's 'Protocol Oriented Programming', but the fundamentals of FP are still the same. You can see see those methods as if they were namespaced functions by the type, rather than global functions overloaded for specific types.

Learn how the basic functions work, and how you can apply them to your day to day code:

- Map
- Filter
- Reduce
- flatMap

[Harken Hallway][harken_twitter] wrote an [excellent post][fp_intro_swift] that gives practical introduction to some of them.

Here are some ways I've changed my programming style by using those more often. It generally boils down to avoiding 'if' statements and loops:

```Swift
// Map for... mapping. You might be surprised how many times you might need this simple concept

struct MyDataModel {
	// Your app model values
}

class MyViewModel {
	init(model: Model) {
		self.model = model
	}
	// ViewModel logic/presentation values
	private let model: MyDataModel
}

let models: [MyDataModel] //Read from somewhere, maybe DB or remote
let viewModels = models.map(MyViewModel.init)
```

```Swift
// Reduce instead of loops. Useful when finding aggregate information
let anySubViewEmpty: Bool = self.subviews.reduce(false) { empty, view
     return empty || CGRectEqualToRect(view.frame, CGRectZero)
}
```

```Swift
// Flatmap with optionals. For example parsing some model array and every parse operation can fail, and it's described with a failable initialializer.

struct MyDataModel {
	// Model values
}

extension MyDataModel {
	init?(json: JSON) {
		// Parsing
	}
}

let jsonItems: [JSON] // Fetched from a remote server
// flatMap in Swift 2.0 has special knowledge of optionals, we take advantage of that
let parsedModels = jsonItems.flatMap(MyDataModel.init)
```

```swift
// Map on optionals

struct User {
    let name: String
    let nickname: String?
}

class UserViewModel {
    init(user: User) {
        self.user = user
    }
    
    var displayName: String {
        func formatNickName(nickName: String) -> String {
            return "Known as \(nickName)"
        }
        // Here we avoid if let by taking advantage of map and nil coalescing operator.
        // Specifies intent with less noise
        return self.user.nickname.map(formatNickName) ?? ""
    }
    
    private let user: User
}
```

There's many times you can benefit from only these basic functions, which are already implemented in the Swift standard library. Starting to apply these standard functions when coding can go a long way to make yourself and your team understand the use and benefit of a more functional or declarative code style. Getting accustomed to using those functions regularly is the first big step to writing more functional code.

### EDIT 15/09/2015: Is 'for' not necessary?

I previously wrote that I liked to use maps for simple loops, like so:

```Swift
// Map instead of loops
let views: [UIView] = // Fill array as needed
views.map { self.addSubview($0) }
// To suppress compiler warning
_ = views.map { self.addSubview($0) }
```

I've recently changed opinion and I think it's better to use standard loops when not producing any result from mapping. We are not increasing readability of the code, but confusing future readers of what is going on. Therefore I don't recommend doing so, so use loops when they are needed.

As you can see any technique can be abused so always keep in mind the benefit of writing code in a particular style.

## Operators

Spend some time reading about FP and you will eventually bump into some crazy-looking operators. Even though I don't think they increase readability of code, it's useful to understand how they work.

If you start working only with functions, you'll need to connect them in somehow. As far as FP is concerned, the connection should be done by, you guessed it, another function. This 'connecting' function will act as an adapter between the output type of the first one, and the input type of the second one.

A typical example is when you have an optional type. Then you have a function that transforms non-optional types and can fail, represented as an optional return. Here's a dummy example:

```swift
// Converts number to string if it is valid
let toValidString: Int -> String? = { number in
    guard number < 10 else { return nil }
    return "Number is small: \(number)"
}

// Converts number to number if it's even
let toEvenNumber: Int -> Int? = { number in
    guard number % 2 == 0 else { return nil }
    return number
}
```

If we want to combine those two functions, the input type of one does not match the output type of the other. The functions do not accept optionals, as it does not make sense to do so.

You can bind those two functions with another function, that will translate the output to the input of the next one:

```swift
func bind<A,B>(from: A, transform: A->B?) -> B? {
	if let from = from {
		return transform(from)
	}

	return nil
}
```

Then combining the operations using bind, the code would be:
```swift
// Outputs "Number is small: 2"
bind(bind(2, transform: toEvenNumber), transform: toValidString)

// Outputs nil
bind(bind(20, transform: toEvenNumber), transform: toValidString)
```

There's one big problem with the previous code: It's very hard to read and know what is going on. The connection function takes over the space and pollutes that we are actually just connecting two other functions together.

Remember that an operator in Swift is just a function. It can take two forms, depending on the operands it works with; A single operand, or two operands. We can then define a cryptic symbol can be defined to express 'bind':

```swift
// Bind operator, also called 'flatmap'
func >>-<A,B>(from: A?, transform: A->B?) -> B? {
    return bind(from, transform: transform)
}
```

Then the two previous examples read a lot better, assuming you know what 'bind' is, and you know how to interpret the operator:
```swift
// Outputs "Number is small: 2"
2 >>- toEvenNumber >>- toValidString

// Outputs nil
20 >>- toEvenNumber >>- toValidString
```

I think the only reason why many custom operators exist in FP languages is because it improves readability of the code. If you know what an operator does - and it's likely you will because they are very common connectors - then you read code and immediately understand the intention of the code. Using them the code should more declarative, with the tradeoff that somebody not used to those operators will thing it is very foreign syntax.

There's other custom operators you will see when reading about FP. There's the two I've encountered most often:

- Bind, also called **FlatMap** in Swift: Expressed as >>>, >>= or **>>-**
- **Apply**, or 'Applicative functors': Expressed as **<*>**

ThoughtBot created two open source projects that are very helpful to understand practical operators used for function composition: [Runes][runes] and [Argo][argo]. Those projects are concise and helped me a lot to grasp some FP concepts, I encourage you to check them.

> **Swift 2.0**: As Swift moves towards removing usage of free functions in favour of methods in protocols and protocol extensions, you'll see less use of these custom operators in the language. But still, it's a nice concept to understand.

# Conclusion

Changing programming habits is hard, but adopting a new way of writing code can be very rewarding if you understand the benefits that it provides. 

I think smaller changes to ways we code are achievable by every team and it's just a matter of communicating and discussing the benefits through code reviews. Ultimately the goal is to make code simpler, clearer and easier to read and maintain.

Given Swift is a multi-paradigm language it's up to the programmer to choose how to write code, so it's important to  explore various ways until we find what we could call 'idiomatic' Swift.

We are all learning so don't hesitate to tell me if I'm wrong or disagree with me. If you have any comments I'd love to [discuss them][twitter].

[part1]: {{site.url}}/blog/2015/08/31/poor-mans-functional-programming/
[small_large]: http://www.johndcook.com/blog/2009/03/23/functional-in-the-small-oo-in-the-large/
[fp_intro_swift]: http://harlankellaway.com/blog/2015/08/10/swift-functional-programming-intro/
[harken_twitter]: https://twitter.com/HarlanKellaway
[twitter]: https://twitter.com/miguelquinon
[swiftanimations]: https://github.com/DarthMike/SwiftAnimations
[cartography]: https://github.com/robb/Cartography
[runes]: https://github.com/thoughtbot/Runes
[argo]: https://github.com/thoughtbot/Argo