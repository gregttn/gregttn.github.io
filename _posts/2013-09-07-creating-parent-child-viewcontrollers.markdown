---
author: gregttn
comments: true
date: 2013-09-07 12:30:30+00:00
layout: post
slug: creating-parent-child-viewcontrollers
title: Creating parent-child viewcontrollers
wordpress_id: 57
categories:
- iOS
---

The MVC pattern lies at the heart of the Cocoa framework and iOS development. It is clearly visible the first time you build/read an iOS application. There is a controller (_UIViewController_), view (storyboard, nib files) and finally model which you define.

There are times when you look at your controller class and think it would be nice to have another controller taking care of that part of the view. This will allow you share responsibility for managing the interactions in multiple classes and make sure that each of the controllers have a specific task at hand. Furthermore, splitting responsibilities across classes will ease future maintenance and development.

If you look further you probably have multiple controllers displayed on the screen already. There are obvious examples - _UITabbarController_ and _UINavigationController_ are displayed together with custom controller you built.
We will build a simple application which just has one button on the main view. This button will be managed by the child view controller.
We need a simple project with one view. I assume you are accustomed with Xcode and able to create simple single view application.

### Create parent controller

The simplest step of all. The example parent controller will ultimately be almost empty. The only responsibility interesting to us is creation of the child view controller.

{% highlight objectivec %}
#import "ViewController.h"
#import "ChildViewController.h"
 
@interface ViewController ()
 
@end
 
@implementation ViewController {
    ChildViewController *child;
}
 
- (void)viewDidLoad
{
    [super viewDidLoad];
    
    // create  child controller with frame
    CGRect childFrame = CGRectMake(0,0, 150, 150);
    child = [[ChildViewController alloc] initWithFrame: childFrame];
 
    //add child view controller to parent
    [self addChildViewController:child];
    [self.view addSubview:child.view];
    [child didMoveToParentViewController:self];
    
    // center the view
    CGSize screenSize = [UIScreen mainScreen].bounds.size;
    child.view.center = CGPointMake(screenSize.width/2, screenSize.height/2);
}
 
- (void)removeChildController {
    [child willMoveToParentViewController:nil];
    [child.view removeFromSuperview];
    [child removeFromParentViewController];
}
 
- (void)didReceiveMemoryWarning
{
    [super didReceiveMemoryWarning];
}
 
@end
{% endhighlight %}


The most import part of the code above is the viewDidLoad method. Firstly, we create frame for the child view controller so that it can put its own view hierarchy on there. The child view controller has a init method that takes a frame. I will explain below why.

Next, the child controller has to be added to the parent’s pool of children. It is very important to follow this pattern so that all the events are automatically forwarded to the child controllers. I mentioned earlier that any controller is responsible for managing its own view hierarchy. In this case the view hierarchy of the child controller has to be added to the parent so that it can be displayed. Last method we call is the _didMoveToParentViewController_. This method notifies child view controller that the operation of adding (or removing) child from the parent was finished.

Lastly, the child view is adjusted so that it displayed in the center of the screen.

### Create child controller

As I mentioned before the child is responsible for managing its own view. The parent controller decides where to place the child view and what size it should be (by initializing it with a frame). You implement it as you would do with any other controller. There are some extra methods needed to support the parent-child relationship, they will be discussed below so don’t worry about them at the moment.

{% highlight objectivec %}
#import "ChildViewController.h"
 
@interface ChildViewController ()
 
@end
 
@implementation ChildViewController {
    CGRect viewFrame;
}
 
- (id)initWithFrame: (CGRect)frame {
    self = [super init];
    
    if (self) {
        viewFrame = frame;
    }
    
    return self;
}
 
- (void)viewDidLoad
{
    [super viewDidLoad];
}
 
- (void)loadView {
    // create view for the controller
    self.view = [[UIView alloc] initWithFrame:viewFrame];
    self.view.backgroundColor = [UIColor darkGrayColor];
    
    // create the button
    UIButton *btn = [UIButton buttonWithType:UIButtonTypeRoundedRect];
    btn.frame = CGRectMake(0,0,self.view.frame.size.width,50);
    [btn setTitle:@"Child button" forState:UIControlStateNormal];
 
    // center btn in the parent view
    btn.center = self.view.center;
    
    [self.view addSubview:btn];
}
 
- (void)didMoveToParentViewController:(UIViewController *)parent {
    NSLog(@"Child controller added/removed from the parent controller");
}
 
- (void)willMoveToParentViewController:(UIViewController *)parent {
    NSLog(@"Child controller will be added/removed from parent controller");
}
 
@end
{% endhighlight %}

As you can see this view controller has an _initWithFrame_ method with takes the frame to which the child should draw its views. It is stored for later use in loadView.

The view hierarchy is build in the _loadView_. This method is called the first time view property of the controller is accessed and it is nil. It means that the controller needs to build it. In our case we create the _UIView_ using the frame that we stored in the init method and assign it to the _self.view_ property. I also adjusted color of the view so that it is clear which part of the screen is managed by parent and which by the child. Further, the _UIButton_ is created and assigned to the center of the view.

There are two methods that need to be discussed:

{% highlight objectivec %}
- (void)didMoveToParentViewController:(UIViewController *)parent
{% endhighlight %}

It is called when the operation of adding the child controller to the parent is completed. The second case when it is called is not discussed in detail in there. It is called when the _removeFromParentViewController_ message is sent to the child.

{% highlight objectivec %}
- (void)willMoveToParentViewController:(UIViewController *)parent
{% endhighlight %}

This is automatically called when the the child is added to the parents pool of children using _addChildViewController_.
It should also be called as a first step when the parent wants to remove the child with a nil controller.

### Removing child controller
In the parent view controller I also added a method to remove the child. I will not discuss it in detail as I think it is quite straighforward.

The final result looks like follows:


[![](/assets/images/2013/09/Screen-Shot-2013-09-07-at-13.25.54.png){: .small_image}](/assets/images/2013/09/Screen-Shot-2013-09-07-at-13.25.54.png)
