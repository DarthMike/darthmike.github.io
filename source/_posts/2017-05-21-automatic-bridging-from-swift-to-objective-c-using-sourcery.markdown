---
layout: post
title: "Automatic bridging from Swift to Objective-C using Sourcery"
date: 2017-05-21 22:30:00 +0100
comments: true
categories: tools meta-programming swift Objective-C
---

I recently read a [great article][Bridging-post] by [Benjamin Encz][encz] about bridging Swift to Objective-C by creating bridging types that can be exposed to the older language. I wondered if his idea can be improved and automated using meta-programming with [Sourcery][Sourcery] by [Krzystof Zablocki][zablocki]. The answer is a very impressive ObjC-like uppercase **YES**.

<!-- more -->

First of all, you should read [Benjamin's post][Bridging-post]. He proposes to use a bridging class that can be instantiated from Objective-C and Swift because it inherits from `NSObject`. The bridging class has properties to get and set the properties of the bridged type.

In this post I'm just going to give an overview of the template and highlight the simpler parts. If you want to jump directly in the code, here's the [project][example], and the [template][template]. The example template bridges struct and enum, but doesn't handle custom protocols to keep it as simple as possible.

## About Sourcery

[Sourcery][Sourcery] is a tool to generate code, using your written templates, using metadata from your production code. In other words, it's a tool to do [metaprogramming][meta] in Swift. If you are not familiar with Sourcery I encourage you to go read the documentation and at least understand the simple examples like generating `Equatable` implementations for your types. Getting familiar with [Stencil][stencil], one of the supported template languages is also fundamental.

## Building the template

We'll need to generate a new class for every struct and enum that we bridge. In order to opt-in to the feature, we use [phantom types][phantom] in order to mark swift types (our custom structs or enums) that can be bridged to Objective-C. Our template will use this protocol to which types to introspect:
```swift
protocol AutoObjCBridgeable {}
```

For example, take Benjamin's example code:
```swift
enum CheckoutOption {
    case creditCard(given: Int)
    case paypal(String)
}

struct ShoppingCart  {
    var items: [String]
    var checkoutOption: CheckoutOption?

    init(items: [String], checkoutOption: CheckoutOption? = nil) {
        self.items = items
        self.checkoutOption = checkoutOption
    }
}
```

We just need to adopt the protocol in an extension for the types, in order to opt-in to code generation with Sourcery:
```swift
extension ShoppingCart : AutoObjCBridgeable {}
extension CheckoutOption : AutoObjCBridgeable {}
```

### Structs vs enums

Structs are straightforward to bridge, as we only need to handle properties. On the other hand, enums are much more involved because we need to take care of handling an arbitrary number of associated values. Fear not, as Sourcery provides all the necessary metadata for our nefarious needs!

We will divide the template in two parts: one loop for structs, one for enums.

```javascript
// Bridging for structs
{% for type in types.implementing.AutoObjCBridgeable|struct %}
{% endfor %}

// Bridging of enums
{% for enum in types.implementing.AutoObjCBridgeable|enum %}
{% endfor %}
```

For structs, we only generate one class that contains the Swift native type. We wrap the type with API to set and get properties, following Benjamin's design. Enumerations will require more classes (see below).

Also bear in mind that when bridging properties in either an enum or a struct, we need to handle differently types that are bridged by ourselves (our custom struct and enums).

### Bridging an empty struct

This is how we start generating classes for every bridgeable struct:

{% raw %}
```javascript
{% for type in types.implementing.AutoObjCBridgeable|struct %}
@objc(XYZ{{type.name}})
class _ObjC{{type.name}} : NSObject {
    // TODO
}
{% endfor %}
```
{% endraw %}

Given a simple, empty struct:
```swift
struct EmptyStruct : AutoObjCBridgeable {
}
```

Let's look at what a the resulting bridging code for the simplest struct would look like; An empty struct should only wrap the type in an Objective-C class, like so:
```swift
@objc(XYZEmptyStruct)
class _ObjCEmptyStruct : NSObject {
    private (set) var emptyStruct: EmptyStruct

    // Initializer to be used from Swift code
    init(emptyStruct: EmptyStruct) {
        self.emptyStruct = emptyStruct
    }

    // Initializer to be used from ObjC code
    override init(){
        self.emptyStruct = EmptyStruct()
    }
}
```

Now for a more useful type, say a struct with some values, like this one:
```swift
struct StructWithSwiftProperties : AutoObjCBridgeable {
    let name: String
    let count: Int
}
```

The bridging code looks like this:
```swift
@objc(XYZStructWithSwiftProperties)
class _ObjCStructWithSwiftProperties : NSObject {
    private (set) var structWithSwiftProperties: StructWithSwiftProperties

    // Initializer to be used from Swift code
    init(structWithSwiftProperties: StructWithSwiftProperties) {
        self.structWithSwiftProperties = structWithSwiftProperties
    }

    // Initializer to be used from ObjC code
    init(
        name: String,
        count: Int,
        valid: Bool,
        mutableValid: Bool,
        someStuff: [String]
    ){
        self.structWithSwiftProperties = StructWithSwiftProperties(
                name: name,
                count: count,
                valid: valid,
                mutableValid: mutableValid,
                someStuff: someStuff
            )
    }

    var name : String {
        return self.structWithSwiftProperties.name
    }

    var count : Int {
        return self.structWithSwiftProperties.count
    }
}
```

The generated code will need to provide read-only properties for the inmutable properties, and read-write properties for the mutable ones. This example only contains simple types, but not other structs or enumerations. Generating those will require to nest bridged classes. You can check the [template code][template] by yourself for the rest of it.


Generating properties for standard Swift types is simple:
{% raw %}
```javascript
{% if variable.type.kind != "struct" and variable.type.kind != "enum" %}
    // Forwarding property for native types
    var {{variable.name}} : {{variable.typeName}}
    {
        get {
            return self.{{type.name|lowerFirstWord}}.{{variable.name}}
        }
        {% if variable.isMutable %}
        set {
            self.{{type.name|lowerFirstWord}}.{{variable.name}} = newValue
        }
        {% endif %}
    }
{% endif %}
```
{% endraw %}

Generating the swift initializer and the wrapped property is also simple:
{% raw %}
```javascript
private (set) var {{type.name|lowerFirstWord}}: {{type.name}}

// Initializer to be used from Swift code
init({{type.name|lowerFirstWord}}: {{type.name}}) {
    self.{{type.name|lowerFirstWord}} = {{type.name|lowerFirstWord}}
}
```
{% endraw %}

Generating the initializer from Objective-C will require to handle structs and enumerations specially, so you can check it yourself in the [template][template].

### Bridging simple enumerations

The template code to bridge enumerations is a bit more complex. We're just going to review what the generated code would look like in this case. Take a simple example:

```swift
enum OtherEnum : AutoObjCBridgeable {
    case a
    case b(EmptyStruct)
}
```

The generated bridging code should contain a class for the enum, and separate classes for every case. We initialize from Objective-C casting from `Any` to the concrete class corresponding to every case. Take note we'll need to handle associated values and because of this the template code is a bit messy.
```swift
@objc(XYZOtherEnum)
class _ObjCOtherEnum : NSObject {
    private (set) var otherEnum: OtherEnum

    // Initializer for Swift code
    init(value: OtherEnum) {
        self.otherEnum = value
    }

    // Initializer for Objective-C code
    init(caseValue: Any) {
        if let _ = caseValue as? _ObjCOtherEnumA {
            self.otherEnum = .a
        } else if let caseValue = caseValue as? _ObjCOtherEnumB {
            self.otherEnum = .b(caseValue.value1.emptyStruct)
        } else {
            preconditionFailure("Value \(caseValue) is not compatible with cases of OtherEnum")
        }
    }
}


// A case of OtherEnum
@objc(XYZOtherEnumA)
class _ObjCOtherEnumA : NSObject {

}

// A case of OtherEnum
@objc(XYZOtherEnumB)
class _ObjCOtherEnumB : NSObject {
    let value1 : _ObjCEmptyStruct

    init(value1 : _ObjCEmptyStruct)
    {
        self.value1 = value1
    }
}
```

## Conclusion

Automating the generation of complex things like bridging is possible with Sourcery. The template I've written doesn't handle all cases of possible code; For example, the template doesn't handle private properties, doesn't take default values for initializers, we don't forward methods, and it doesn't handle properly structs as associated values of enums. The template also doesn't bridge protocols.

I stopped here on purpose, as this generated code should be temporary until a migration eventually happens. In general, interaction from Objective-C to Swift should be kept to a minimum.

I'm very surprised that such complexity can be automated using Sourcery, and I really love the concept of the tool. If you never used it, it's a very useful addition for your Swift toolbelt.

If you have any feedback, please open an issue in the [example repository][example], or reach me on [twitter][twitter]. I'd love to hear your thoughts!

[Bridging-post]: http://blog.benjamin-encz.de/post/bridging-swift-types-to-objective-c/
[Sourcery]: https://github.com/krzysztofzablocki/Sourcery
[encz]: https://twitter.com/benjaminencz
[zablocki]: https://twitter.com/merowing_
[meta]: https://en.wikipedia.org/wiki/Metaprogramming
[stencil]: https://github.com/kylef/Stencil
[phantom]: https://www.objc.io/blog/2014/12/29/functional-snippet-13-phantom-types/
[template]: https://github.com/DarthMike/AutoObjCBridgeable/blob/master/Templates/AutoObjCBridgeable.stencil
[example]: https://github.com/DarthMike/AutoObjCBridgeable
[twitter]: https://twitter.com/miguelquinon
