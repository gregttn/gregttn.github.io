---
author: gregttn
comments: true
date: 2013-11-07 22:35:08+00:00
layout: post
slug: singleton-pattern-objective-c
title: Singleton Pattern Objective-C
wordpress_id: 90
categories:
- iOS
---

So you want to have a singleton object in you iOS/Mac app? This is actually really simple to achieve. I will not discuss cons and pros of the pattern itself. There is plenty of material on the internet on that topic. Let's get started and do some coding.

1. Create class for you singleton.
2. Add class method to access shared instance of the object:

{% highlight objectivec %}
#import <Foundation/Foundation.h>
 
@interface GTTSingleton : NSObject
+ (GTTSingleton*)sharedInstance;
@end
{% endhighlight %}

3. Implement the class method:

{% highlight objectivec %}
#import "GTTSingleton.h"

@implementation GTTSingleton
+ (GTTSingleton*)sharedInstance {
    static GTTSingleton *singletonInstance;
    static dispatch_once_t token;
    
    dispatch_once(&token, ^{
        singletonInstance = [[GTTSingleton alloc] init];
    });
    
    return singletonInstance;
}
@end
{% endhighlight %}

The implementation is quite simple. The first thing I do is declare the static instance of the class itself. Note that this is not initialised at this point. If I initialised it in here I would loose the behaviour I want as each time the method is called new object would be allocated. The second variable I need is a *dispatch_once_t* token which is used to check if the following operation was previously executed. It is important to have it declared as static. Lack of it might lead to weird behaviour. Finally I use GCD in form of *dispatch_once*. It performs work synchronously and guarantees that whatever operation you specified in a block executes only once. In our case we initialise the *GTTSingleton* object. At the end of the method I return the singeltonInstance knowing that it was properly initialised.
