---
author: gregttn
comments: true
date: 2013-12-28 14:55:04+00:00
layout: post
slug: uikit-dynamics
title: UIKit Dynamics
wordpress_id: 96
categories:
- iOS
---

Recently, I was playing with UIKit Dynamics. I found it to be very interesting and easy to use framework.

Let's start from the beginning. What is UIKit Dynamics? Well, in simple words it is a physics engine which allows you to attach physic related properties to UI elements. Knowing from my own example I believe that there are people that believe that physics belong to games not to UI for normal apps. Wrong. By using physics you can build an UI that feels real. You can make you views react to touch, gesture, orientation change as if they were a real life objects. Well, that's really cool!

While exploring the UIKit Dynamics I build a simple application that I hope to walk through it in this post. It is a very basic usage of the framework. I believe that it will provide you with some knowledge that will help you explore the power of UIKit Dynamics. Let's get started

First of all study the code. The explanation will follow.

{% highlight objectivec %}
#import "GTTDynamicViewController.h"
 
@interface GTTDynamicViewController ()
@property (strong, nonatomic) UISegmentedControl *fallingControl;
 
@property (strong, nonatomic) UIDynamicAnimator *animator;
@property (strong, nonatomic) UIGravityBehavior *gravity;
@property (strong, nonatomic) UICollisionBehavior *collision;
@end
 
@implementation GTTDynamicViewController
 
- (void)viewDidLoad
{
    [super viewDidLoad];
    
    _fallingControl = [[UISegmentedControl alloc] initWithItems:@[@"Yes", @"No"]];
    _fallingControl.frame = CGRectMake(0, 0, 200, 35);
    _fallingControl.center = CGPointMake(self.view.center.x ,0);
    _fallingControl.selectedSegmentIndex = 1;
    
    [self.view addSubview:_fallingControl];
    [self setupDynamics];
}
 
- (void)setupDynamics {
    _animator = [[UIDynamicAnimator alloc] initWithReferenceView:self.view];
    
    _gravity = [[UIGravityBehavior alloc] initWithItems:@[_fallingControl]];
    
    _collision = [[UICollisionBehavior alloc] initWithItems:@[_fallingControl]];
    _collision.translatesReferenceBoundsIntoBoundary = YES;
    
    [_animator addBehavior:_gravity];
    [_animator addBehavior:_collision];
}
 
- (void)didReceiveMemoryWarning
{
    [super didReceiveMemoryWarning];
}
 
@end
{% endhighlight %}

You can copy the code into XCode and try it out. You'll see that it only has one segmented control which is placed at the top of the view. Once everything is setup and the view displays the segmented control falls down. When it reaches the bottom edge of the screen it bounces gently. As I said it is a very simple app.

Let's look at the properties. It should come of no surprise at this point that there is a _UISegmentedControl_ in there. Please note, that I do not use storyboard for this example, rather I build the control myself programmatically. After this we have three properties that are of an interest to us:



	
  * [UIDynamicsAnimator](https://developer.apple.com/library/IOS/documentation/UIKit/Reference/UIDynamicAnimator_Class/Reference/Reference.html)




Basically,  this is an engine that gives elements physics capabilities. It also keeps track of all behaviours (see below) that you wish to use with you UI elements.






	
  * [UIGravityBehavior](https://developer.apple.com/Library/ios/documentation/UIKit/Reference/UIGravityBehavior_Class/Reference/Reference.html)




I think the name says it all. This class adds gravity related properties to your elements. This allows out UISegmentedControl to fall from the top of the screen.






	
  * [UICollisionBehavior](https://developer.apple.com/library/ios/documentation/uikit/reference/UICollisionBehavior_Class/Reference/Reference.html)




Again, name says it all. This allows elements to collide with each other. In our example UISegmentedControl collides with edges of the screen.


At this point you should be familiar with what the certain objects are responsible for. Turn you attention to _setupDynamics _method.

First of all I create new _UIDynamicAnimator_. As a parameter I pass the root view of the elements I wish to animate. Since _self.view_ is the root view I pass it as a reference view.  Next we create _UIGravityBehavior_. Every behaviour has to be aware of the items it has to act on. In this case it is the _UISegmentedControl_. You can of course just init the behaviour and add items later (see Apple docs for details). After that, I build the _UICollisionBehavior_. The init process is exactly the same as with gravity. The next line  sets the property _translatesReferenceBoundsIntoBoundary._ You might ask what is this. Well, this is the way to tell collision behaviour to treat the edges of the screen as the collision boundary so that UI elements will bounce of it as opposed to falling of the screen. If you feel like experimenting, comment that line out and you will see that the _UISegmentedControl_ falls of the screen.

The last thing I do is register the behaviours with the animator. This is very important step. If you forget to do that the behaviour you attached to you UI element will not work.

That's it. There is of course more behaviours that you can apply and more properties you can specify. In this post I set out to provide you with the basics of using UIKit Dynamics. Hopefully, you took something out of it. Apple provided an example app that has some interesting examples that you might want to explore:

[https://developer.apple.com/library/IOs/samplecode/DynamicsCatalog/Introduction/Intro.html](https://developer.apple.com/library/IOs/samplecode/DynamicsCatalog/Introduction/Intro.html)

Happy coding!
