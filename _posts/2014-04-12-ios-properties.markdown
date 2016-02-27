---
author: gregttn
comments: true
date: 2014-04-12 14:56:12+00:00
layout: post
slug: ios-properties
title: iOS Properties
wordpress_id: 152
categories:
- iOS
---

When starting with Objective-C many people find properties confusing. Others just use them without really understanding what they are and how they work. In the following sections I intend to describe what the properties are and how to use them.


Properties were formally introduced as a part of the language in release 2.0. They are generated using the @property directive. This makes the compiler generate the accessors for the given property so that you do not have to. Each of the properties you define is backed by an instance variable (also added by the compiler). To access the backing variable prefix your property name with “_”. Let’s have a look at simple example.


#### GTTCar.h

{% highlight objectivec %}
@interface GTTCar : NSObject
  @property (strong, nonatomic) NSString* mark;
  @property (strong, nonatomic) NSString *model;
    
  -(id)initWithMark:(NSString*)mark andModel:(NSString*)model;
    
@end
{% endhighlight %} 



####  GTTCar.m



{% highlight objectivec %}
#import "GTTCar.h"
@implementation GTTCar
  
  -(id)initWithMark:(NSString*)mark andModel:(NSString*)model {
    self = [super init];
    
    if (self) {
      self.mark = [mark copy]; // a
      // [self setMark:[mark copy]]; // b
      _model = [model copy]; // c
    }
    return self;
  }
    
@end
{% endhighlight %} 



This class has two properties defined: mark and model. They both have two attributes strong and nonatomic  (I will discuss them shortly). Have a look at the initWithMark:andModel: method. Line “a” uses self.mark to assign the value to a property. What this does is invoke the actual setter. It is equivalent to line “b” which explicitly uses the setter. Notice that this was generated for us. On the line “c” we write directly to the _model variable. This is the backing instance variable for the property.


If you are not happy with the fact that the backing ivar is prefixed with the “_” there is a way to change that. You can use the @synthesize directive in the implementation file as follows:

{% highlight objectivec %}
    @synthesize model = model_;
{% endhighlight %} 

It will force the compiler to change the default name of the ivar. This is not recommended practice as it might make your code confusing to others. You were warned.


I mentioned before that the compiler generates the accessor methods for us. You might ask why. Objective-C follows very strict naming patterns

  * the setter is always of format setXXX
  * the getter is just the name of the variable


The use of properties enforces that this strict naming policies are followed.


The accessors methods and the backing instance variable are generated a phase called autosynthesis during compile time. Of course there is a way to stop the compiler generating these for you. You can do it by using the @dynamic directive in your implementation:

{% highlight objectivec %}
    @dynamic model;
{% endhighlight %}

This will tell the compiler NOT TO generate the accessors and backing ivar for a particular property. One place you can see it used is in Core Data NSMangedObject subclasses.

### Property Attributes

You can assign attributes when declaring a property which allow you to configure it. In the example above we had strong and nonatomic. The attributes fall into four groups.

#### Memory Management
	
    * strong
This creates an owning relationship with the object. When assignment  is made the value being assigned is retained. The property is valid as long as the owning object needs it.

    * assign
This is used for non-object properties like CGRect

    * weak
Opposite to strong attribute this does not create (retain) an owning relationship. When whatever object owns the value assigned to weak property is destroyed the weak value will become nil.

	
    * copy
copy attribute is similar to strong. The difference is that before the object is retained it is copied.

	
    * unsafe_unretained
Shouldn’t be used very often. It is similar to weak property, but it does not nil the value when the owning object is destroyed.

#### Access modifiers

    * readwrite (default)
Compiler will generate both the getter and setter.

	
    * readonly
It prevents setter from being generated, which in turn prevents assignment when using dot notation.
	
#### Atomicity
	
    * atomic (default)
When set this attribute provides locking on accessor methods

	
    * nonatomic
Does not provide thread safety
	
#### Other
	

    * setter
You can specify the name of the setter that you want using setter=<methodName>

	
    * getter
You can specify the name of the getter that you want using getter=<methodName>

### Overwriting Property Attributes


You might want to set a property readonly for the users of your class. This is good because it makes your object immutable. At this point you might notice that if you declare the property readonly you will not be able to use it in your implementation file. It is possible to re declare the property so that you can use it.

#### Revised GTTCar.h

{% highlight objectivec %}    
@interface GTTCar : NSObject
  @property (copy, readonly, nonatomic) NSString *mark;
  @property (copy, readonly, nonatomic) NSString *model;
    
  -(id)initWithMark:(NSString*)mark andModel:(NSString*)model;
    
@end
{% endhighlight %}

Notice that both model and mark are created as readonly!


#### Revised GTTCar.m

{% highlight objectivec %}    
@interface GTTCar ()
  @property (copy, readwrite, nonatomic) NSString* mark;
  @property (copy, readwrite, nonatomic) NSString *model;
@end
    
@implementation GTTCar
    
  -(id)initWithMark:(NSString*)mark andModel:(NSString*)model {
    self = [super init];
    if (self) {
      self.mark = mark;
      self.model = mark;
    }
    
    return self;
  }
@end
{% endhighlight %}

Notice that at the top we have class extensions which re declares the properties to be readwrite. That's it now you can use the properties in your code.


