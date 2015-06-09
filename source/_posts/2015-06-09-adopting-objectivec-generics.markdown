---
layout: post
title: "Adopting Objective-C generics"
date: 2015-06-09 22:53:14 +0100
comments: true
categories: [ios, apple, tools]
---
This year in [WWDC](https://developer.apple.com/wwdc/), Apple introduced Objective-C ‘lightweight’ [generics][generics] for XCode7. This builds upon the improvements to Objective-C to document the code and improve the interoperability with Swift. I wrote previously about [nullability annotations][nullability] and I continue to be delighted by these changes to the language. Here’s what I think.

<!-- more -->

# ‘Lightweight’ generics

Let me say it before we begin, the syntax is simple so we will not see (indispensable) sites like [this one][fbs]. This addition to the language allows to specify the compile-time type of the objects contained in **collections**. It looks like this:

```objc
@interface Wheel : NSObject
@property (nonatomic, strong) UIColor *color;
@end

@interface Vehicle : NSObject
@property (nonatomic, copy) NSArray<Wheel *> *wheels;
@end
```

Note the brackets on the declaration of the NSArray property. As you can see it works with your types, and it lets the compiler check the types for you. You get warnings which can let you know something is amiss when an API:

```objc
Wheel *wheel = [[Wheel alloc] init];
wheel.color = [UIColor blackColor];
    
Vehicle *car = [[Vehicle alloc] init];
car.wheels = @[wheel];
    
// All is fine for normal case
Wheel *w = car.wheels[0];
UIColor *color = w.color;

// Will generate a warning “Incompatible pointer types initializing ...”
NSString *str = car.wheels[0];
```

So now you can remove all the pesky comments to tell your colleagues and future self what these collections contain. Be aware that the generated code does not change, as we will see in a few moments. And most importantly, you can also annotate your own APIs! This is very good.

All Foundation collections support generics: NSArray, NSMutableArray, NSSet, NSMutableSet, NSOrderedSet, NSMutableOrderedSet, NSDictionary, NSMutableDictionary, NSHashTable, NSMapTable.

#Kindof

The compiler will compare direct pointer types. This works correctly many times, but sometimes you want to still use the behaviour of `id` but restricted to a base class. This is technically not safe, but is code you that you normally write. In this case you can use `kindof`. Let’s add a subclass to our previous models. A handy square wheel:

```objc
@interface SquareWheel : Wheel
@property (nonatomic, assign) double size;
@end
```

Now that’s how you would use it sometimes, assuming that you will get the subclass:

```objc
SquareWheel *squarewheel = [[SquareWheel alloc] init];    
squarewheel.size = 10;
    
Vehicle *car = [[Vehicle alloc] init];   
car.wheels = @[squarewheel];
    
// Generates warning, as pointer types do not match.
SquareWheel *w = car.wheels[0];
```

In this case you can annotate the types with the `kindof` keyword, to let the compiler that you want to freely assume that subclasses of the specified type are also valid:

```objc
@interface Vehicle : NSObject
@property (nonatomic, copy) NSArray<__typeof Wheel *> *wheels;
@end
```

# In your own code

You can also annotate APIs in your code. Let’s see an example:

```objc

@interface MyDataSource<ObjectType> : NSObject
- (ObjectType)modelAtIndex:(NSUInteger)index;
- (void)addModel:(ObjectType)model;
@end

@interface MyDataSource()
@property (nonatomic, strong) NSMutableArray *items;
@end

@implementation MyDataSource
- (instancetype)init {
    self = [super init];
    
    if (self) {
        _items = [NSMutableArray array];
    }
    
    return self;
}

- (id)modelAtIndex:(NSUInteger)index {
    return self.items[index];
}

- (void)addModel:(id)model {
    [self.items addObject:model];
}

@end

```

The annotations are only for the API, and then your implementations are normal code. Then using the annotated API the compiler will check the static types:

```objc
MyDataSource<NSString *> *stringDS = [[MyDataSource alloc] init];

// Retrieving and adding strings to the datasource. OK
[stringDS addModel:@“hi!”];
NSString *model = [stringDS modelAtIndex:0];
    
MyDataSource<Vehicle *> *vehicleDS = [[MyDataSource alloc] init];

// Generates warning. Pointer types do not match    
[vehicleDS addModel:@“Audi”];
NSString *string = [vehicleDS modelAtIndex:0];
```

# Caveats

The language didn’t change, and I would argue this is good. Apple didn’t hack it just for this feature. So you can still crash at runtime if your code works around this. 

Let’s try to break it. A crashy example:

```objc
    MyDataSource<Vehicle *> *vehicleDS = [[MyDataSource alloc] init];
// NOTE: Adding NSString in a contrived way. But REAL code is sometimes like that in even more twisted ways ;)
    NSString *v = @“Audi”;
    Vehicle *vh = (Vehicle *)v;
    [vehicleDS addModel:vh];

    Vehicle *crashyVehicle = [vehicleDS modelAtIndex:0];
// Will crash here, as even though it compiled, actual
// runtime type is string.
    if (crashyVehicle.wheels.count) {
        // Do stuff
    }
```

# Adopting generics for existing projects

As with nullability, adopting this language changes will be tedious. I think the best candidates for this are the lower level parts of your code, specially domain and model objects. This will have higher impact on your codebase with the least amount of effort.

# Conclusion

Objective-C advances to grudgingly make way to Swift. Apple is going full-steam with Swift and this change is more in favour of that language than of Objective-C itself.

But as many of us need to work with ‘legacy’ Objective-C code, ‘lightweight generics’ is nevertheless a welcomed addition.

I will mention again this is not a full ’generics’ implementation for Objective-C, but rather annotations for the compiler to check your API usage, and intention. It also changes how Swift sees the Objective-C code and improves interoperability between the languages. A win win situation.

[generics]: https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/InteractingWithObjective-CAPIs.html#//apple_ref/doc/uid/TP40014216-CH4-ID35
[fbs]: http://fuckingblocksyntax.com/
[nullability]: {{site.url}}/blog/2015/04/17/adopting-nullability-annotations/