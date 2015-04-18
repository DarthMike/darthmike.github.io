---
layout: post
title: "Adopting nullability annotations"
date: 2015-04-17 23:39:03 +0100
comments: true
categories: [objective-c, iOS, code style]
---

Apple introduced nullability annotations to Objective-C compiler from [XCode 6.3](https://developer.apple.com/swift/blog/?id=25). I was really surprised by this change, and welcomed it with open arms when I saw the release notes. My first thought was: Objetive-C is learning from Swift! I want to share my 2 cents on this new annotation and what it means for more modern Objective-C code.

<!-- more -->

#The new annotations

There's not much to these new annotations. You can check all the details from Apple's [blog post](https://developer.apple.com/swift/blog/?id=25), but I will summarise here:

- There's two new annotations for (obviously) pointer types which can be nil or NULL: `__nullable` and `__nonnull`
- These annotations can be used anywhere you can use the C `const` keyword
- Within method and property declarations, a non-underscore annotation can be used (`nullable` and `nonnull`)
- You can annotate whole parts of a file to assume non null, with `NS_ASSUME_NONNULL_BEGIN` `NS_ASSUME_NONNULL_END`

That's it.

#What's the catch?

These annotations allow you to express your intent when defining an interface. It makes APIs clearer and reduce the need for documenation specifying nil behaviour. When used from Swift code, it allows the interface to 'transform' as needed to express nullability in Swift terms; That is no more implicitly unwrapped optionals and declared non-nil references, and simple optionals.

But you need to understand that the language (Objective-C) hasn't change, and that these annotations don't change the runtime behaviour of the code at all. In other words: The generated code with and without annotations is the same.

So the compiler will generate warnings when the API is misused. *Compiler. Is. Always. Clever.* Is it in this case?

##Indirect nil values

```objc
// Your object

NS_ASSUME_NONNULL_BEGIN

@interface ViewController : UIViewController
@property (nonatomic, copy) NSString *nonNullString;
@end

NS_ASSUME_NONNULL_END

// Then you use it as:
NSString *aString = [NSString stringWithFormat:@"helloworld %.1f",1.0];
aString = nil;
controller.nonNullString = aString; // No warning. Generated code does not change.
controller.nonNullString = nil; // Warning
```
Compiling the previous code, generates a warning for a nonnull property only when you set a **direct** nil value. If you set an **indirect** nil value, the compiler happily accepts this without any warning. This may surprise you at first, so you need to be aware of this.

I think it could improve though: [rdar://problem/20596086](http://openradar.appspot.com/20596086).

##Weak properties

The typical delegate pattern in Objective-C, annotated:

```objc
NS_ASSUME_NONNULL_BEGIN

@interface ViewController : UIViewController

// Note it's a weak property and compiler does not complain.
// Anyway the property will be nullified by the runtime as it has always been. Annotations don't affect generated code.
@property (nonatomic, weak) id delegate;

@end

NS_ASSUME_NONNULL_END

// Using it:
id delegate = [[NSObject alloc] init];
ViewController *controller = [[ViewController alloc] initWithNibName:nil bundle:nil];
controller.delegate = delegate;
delegate = nil;  // controller.delegate is nil as expected. Generated code does not change.
controller.delegate = nil; // Generates warning at compile time
```

So as you can see, you can annotate a weak pointer as `nonnull` but obviously it is a nullable value. The generated code is not changed and runtime behaviour remains the same. But the compiler (incorrectly) warns you only when you set a direct value to nil.

I think this could also improve: [rdar://problem/20596408](http://openradar.appspot.com/20596408).

#Adopting nullability for existing projects

As with any language or compiler changes, adopting these new annotations can be tedious, specially for large codebases. I think the strategy to follow is to incrementally annotate APIs for different sections of your code.

If you build and application, the lower layers and the models are the place to start annotating right away. All UI code can wait and can be annotated at a later time. Be also aware that even Apple's headers are not annotated yet. With the notable exception of ResearchKit. Expect this to change this summer ;)

If you are building a framework, my guess is that the best place to start annotating is the API. Then working out the annotations to the internal code is a work that can progressively be done in smaller chunks.

#Conclusion

Nullability annotations are a very important addition to Objective-C as a language. There are some minor 'Gotchas' and an adoption work which is very manual. But I think that every iOS Developer should start using these, and modernizing the code as soon as possible.

Be aware that annotations don't turn Objective-C nil semantics into a Swift-like optional. It's just a tool to clarify intentions and compiler does not do too much checking for you.

Well, you still have time till [next set of breaking changes](https://developer.apple.com/wwdc/).
