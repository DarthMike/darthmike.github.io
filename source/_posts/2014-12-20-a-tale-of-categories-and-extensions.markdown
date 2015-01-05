---
layout: post
title: "A tale of categories and extensions"
date: 2014-12-20 21:37:17 +0000
comments: true
categories: [swift, objective-c, iOS, code style]
---
I’ve gradually been changing my code style favouring (*Obj-C*) categories and (*Swift*) extensions a lot more. I’ve been inspired by my other lover (*Ruby*). Why? Readability and code organisation.
<!-- more -->

#Rubyisms

Let me explain my source of inspiration. I’ve been coding with Ruby from time to time this whole year, and one of the features I really like is **opening classes**. 

As a matter of fact, classes in Ruby are never closed. Any file can add to a class declaration whatever it sees fit. You can add methods, nested classes, and properties. This is often used to either add functionality, or to add more expressivity to the code.

A classic example:
```ruby
class Float
	def dollars
		self	
	end

	def euro
		0.817 * self
	end
end
```

So you can express:
```ruby
price = 18.9
euro_price = price.euro
#instead of, for example
price = 18.9
euro_price = self.euro_to_dollars * price
```

The rules for this in Ruby are simple, you can add to existing classes and modify methods and properties. Opening a class and modifying it [can create serious problems](http://www.infoq.com/articles/ruby-open-classes-monkeypatching), but this is the nature of the language. In Ruby you can change even essential parts of the language and the standard libraries.

#The patterns
From now on I will be giving examples using *Swift extensions*. You can apply the examples to Objective-C categories, but bear in mind that **they are not exactly same**, and **they don’t offer the same functionality** - More on that just a bit later.

Please don’t confuse *Swift extensions* with *Objective-C class extensions*. There is no swift counterpart to *Objective-C class extensions*.

So how do I like to (*ab-*)use extensions?
##Factory methods for 3rd party code
Let’s say you are using a 3rd party library. The library offers an awesome control and it is very customisable:
```swift
//File MQAwesomeControl.swift in 3rd party framework
import UIKit
	
public class MQAwesomeControl : UIControl {
    public var zoomEnabled: Bool = false
    public var colouringEnabled: Bool = false
    // More specific customisation options
}
```

But in your application you configure that control in a very specific way, from many different parts of your UI code. Instead of creating a *configurator* object with static methods, or specific *configuration* methods on your types,  you can create an extension on the 3rd party type to offer more API and specific configuration for your application. This keeps your code DRY and separates the configuration of 3rd party types from your code.

```swift
//File MQAwesomeControl_MyApp.swift in your app
extension MQAwesomeControl {
    class func myapplicationControl() -> MQAwesomeControl {
        var control = MQAwesomeControl()
        control.zoomEnabled = true
        control.colouringEnabled = true
        return control
    }
}
```

##Semantic separation of APIs
This is possibly the best known use of this feature. The only drawback is that you can’t add stored properties, so API extensions only containing methods are the best candidates.

```swift
class Car {
    init(brand: String) {
        self.brand = brand
    }
    
    var brand: String
}
//MARK: Steering
extension Car {
    func accelerate() {
        //..
    }
    
    func decelerate() {
        //..
    }
}

//MARK: Reparing
extension Car {
    internal var needsRepairing: Bool {
        return self.motorNeedsRepairing || self.wheelsAreBroken
    }
    
    internal func repair() {
        if !self.needsRepairing {
            return
        }
        
        // Actually repair
    }
    
    private var motorNeedsRepairing: Bool {
        return false // Here do your calculations for motor
    }
    
    private var wheelsAreBroken: Bool {
        return false // Here do your calculations for wheels
    }
}
```
##Protocol adoption
You can interface two disparate types, in different files, by adopting a protocol in an extension, rather than in the type declaration.

```swift
//File Person.swift
class Person {
    var name: String?
    var surname: String?
    var friends: [Person]?
}

//RelatedEntities.swift
protocol RelatedEntities {
    var similarEntities: [RelatedEntities] {get}
}

//Adoption of protocol in Person_Extensions.swift
extension Person: RelatedEntities {
    var similarEntities: [RelatedEntities] {
        if let friends = self.friends? {
            return friends
        }
        
        return []
    }
}
```
##UI styling
Instead of relying on a gigantic stylesheet-type file, where all appearance setup is made, you can use extensions specific to your application to style UI components. Here I use the previous pattern of adopting a protocol in an extension:

```swift
//Protocol in MyApplicationStyles.swift
protocol MyAppliationStyling {
    func setupAppearance()
}

//Extension, possibly in the same file as protocol
extension UITableView: MyAppliationStyling {
    func setupAppearance() {
        UITableView.appearance().separatorColor = UIColor.darkGrayColor()
    }
}

//Usage, on Stylesheet.swift
class Stylesheet {
		//…
    func customiseAppearance() {
        UITableView.setupAppearance()
				//etc
    }
		//…
}
```

Additionally, you can create extensions to reflect your designer’s stylesheet.

```swift
extension UIColor {
    class func brandColor() -> UIColor {
        return UIColor.yellowColor()
    }
    
    class func backgroundColor() -> UIColor {
        return UIColor.whiteColor()
    }
    
    class func highlightColor() -> UIColor {
        return UIColor.redColor()
    }
}
```
##Extracting specific logic for readability
Sometimes you may want to split a slightly complex method into different private methods. 

If your logic requires manipulating a 3rd party or framework type, then you can extract that part of logic into an extension of the 3rd party type instead of extracting it into another method in your type.

#The language details

##Objective-C categories
Objective-C categories offer similar functionality as Ruby’s open classes. You can override existing methods and, using the runtime, you can ‘inject’ your code between the previous implementation and yours. They are very flexible, but unfortunately very dangerous.

##Swift Extensions

In Swift, the programmer can extend not only classes, but enums and structs. Extensions in Swift are safer because they are namespaced to the module they belong to, and you can’t override existing behaviour.

They are less flexible because you can’t override existing behaviour, and you don’t have access to the runtime to ‘plug into’ existing code.

##Shooting everybody on the foot

Why did I write about alternatives to do the same thing on 3 different languages?

There is very good reasons to take categories and opening classes as a bad practice. It’s very easy to misuse and abuse, creating problems for any users of your code, and breaking functionality with newer releases of OS and libraries. I’ve already mentioned before safety problems of categories. 

Please don’t do this in Objective-C:
```objc
//PLEASE DON’T DO THIS!
@implementation UIView (MyCategory)
- (void)dealloc {
	//Here I do my stuff, and I break existing dealloc for UIView AND subclasses
	//Compiler and runtime don’t complain, until you get other crashes, or your code misbehaves…
}

// Other methods
@end
```

If you are using Objective-C you should prefix the category names **and** the method names with your project prefix. This will ensure that no other code clashes with yours at run-time. This is [standard practice](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/CustomizingExistingClasses/CustomizingExistingClasses.html#//apple_ref/doc/uid/TP40011210-CH6-SW1) for any competent Obj-C developer.

What is not so well understood is that you can start changing method implementations from categories in any class, meaning you can do exactly what Ruby does with open classes. If you start swizzling implementations and many libraries do the same, there’s high risk that there will be undefined behaviour. Tshe code starts to be hard to understand because of too much runtime magic.

Even though you are on the safe side by adhering to best practices and not abusing the runtime. The same best practices sometimes pollute the beauty of categories. If you need to prefix every method name, the code does not look so clean and elegant.

Swift offers the best balance with extensions. Thus I think it is perfect for a very *aggressive* use of extensions, as the code will fail to compile when methods and properties have the same name across classes and extensions. The extensions are implicitly namespaced in different modules, so no clash will happen between libraries and between libraries and application code.

#Conclusion

I think extensions is one of the best features of Swift, and a vast improvement over Objective-C categories. I have shown you examples of how you can use extensions for your advantage.

The best feature of Swift extensions, in my opinion, is safety. All types and extensions are implicitly namespaced under a module, per-target in Xcode. This means all complex clashes of similar additions between different modules (libraries, application) are gone.

Many developers would say that Swift extensions are not so flexible as Objective-C categories. Not having access to the runtime, you can’t exchange method implementations to add behaviour or to capture method executions for your own (evil) needs. While this is true, many times this flexibility is limited to small hacks, debugging or proof of concept prototypes. You can still Objective-C for that if you wish.

If you have more ideas on how to use extensions for fun and profit, please [tell me](https://twitter.com/miguelquinon)!