---
author: gregttn
comments: true
date: 2013-11-03 12:58:33+00:00
layout: post
slug: adding-core-data-to-a-project
title: Adding Core Data to a project
wordpress_id: 81
categories:
- iOS
---

Recently I started new project in XCode using single view application template. After spending some time on setting it up I realized that this template does not add Core Data support by default. Adding is relatively simple and most of it is boilerplate code that you need to put in your AppDelegate. Below you can find instructions to follow when manually creating SQLite Core Data store for your app.

**NOTE:** Before starting make sure that you added CoreData.framework to your project libraries.

### Add Core Data Model to your project

First thing you have to do is to go “File > New > File…” (or cmd + n). In the menu that pops up select Core Data under iOS heading and then “Data Model” as follows:


[![Screen Shot 2013-11-02 at 20.26.13](/assets/images/2013/11/Screen-Shot-2013-11-02-at-20.26.13.png)](/assets/images/2013/11/Screen-Shot-2013-11-02-at-20.26.13.png)


Give name to your data model and place it in your project directory. It should now be visible in the project navigator.

### Add properties to AppDelegate.h

You need three objects in order to use Core Data:



	
  * [NSManagedObjectContext](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSManagedObjectContext_Class/NSManagedObjectContext.html)


Manages objects associated with it. Each object can exist only in one context.

	
  * [NSManagedObjectModel](https://developer.apple.com/library/mac/documentation/cocoa/reference/CoreDataFramework/Classes/NSManagedObjectModel_Class/Reference/Reference.html)


Contains schema for entities (model objects) you defined to be used in your application.

	
  * [NSPersistentStoreCoordinator](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/CoreDataFramework/Classes/NSPersistentStoreCoordinator_Class/NSPersistentStoreCoordinator.html)


Provides a way to interact with the underlying data store of your choice.

If you don’t know what these are supposed to do then do consult the Apple documentation it will make your life easier in a longer run.

You will also need a method to provide the path to the documents directory:

{% highlight objectivec %}
- (NSURL *)applicationDocumentsDirectory
{% endhighlight %}

Here is how your header file should look like:

{% highlight objectivec %}
#import <UIKit/UIKit.h>
#import <CoreData/CoreData.h>
 
@interface AppDelegate : UIResponder <UIApplicationDelegate>
 
@property (strong, nonatomic) UIWindow *window;
 
@property (readonly, strong, nonatomic) NSManagedObjectContext *managedObjectContext;
@property (readonly, strong, nonatomic) NSManagedObjectModel *managedObjectModel;
@property (readonly, strong, nonatomic) NSPersistentStoreCoordinator *persistentStoreCoordinator;
 
- (NSURL *)applicationDocumentsDirectory;
 
@end
{% endhighlight %}

### Override accessor methods for objects.

There is certain setup required for each of the objects which you declared before. Have a look at the following code (explanations to follow):

{% highlight objectivec %}
#import "AppDelegate.h"
 
 
@implementation AppDelegate
 
@synthesize managedObjectContext = _managedObjectContext;
@synthesize managedObjectModel = _managedObjectModel;
@synthesize persistentStoreCoordinator = _persistentStoreCoordinator;
 
#pragma mark - CoreData
- (NSManagedObjectContext *)managedObjectContext
{
    if (_managedObjectContext != nil) {
        return _managedObjectContext;
    }
    
    NSPersistentStoreCoordinator *coordinator = [self persistentStoreCoordinator];
    
    if (coordinator != nil) {
        _managedObjectContext = [[NSManagedObjectContext alloc] init];
        [_managedObjectContext setPersistentStoreCoordinator:coordinator];
    }
    
    return _managedObjectContext;
}
 
- (NSManagedObjectModel *)managedObjectModel
{
    if (_managedObjectModel != nil) {
        return _managedObjectModel;
    }
    NSURL *modelURL = [[NSBundle mainBundle] URLForResource:@"ExampleModel" withExtension:@"momd"];
    _managedObjectModel = [[NSManagedObjectModel alloc] initWithContentsOfURL:modelURL];
    return _managedObjectModel;
}
 
- (NSPersistentStoreCoordinator *)persistentStoreCoordinator
{
    if (_persistentStoreCoordinator != nil) {
        return _persistentStoreCoordinator;
    }
    
    NSURL *storeURL = [[self applicationDocumentsDirectory] URLByAppendingPathComponent:@"ExampleDB.sqlite"];
    
    NSError *error = nil;
    _persistentStoreCoordinator = [[NSPersistentStoreCoordinator alloc] initWithManagedObjectModel:[self managedObjectModel]];
    
    if (![_persistentStoreCoordinator addPersistentStoreWithType:NSSQLiteStoreType configuration:nil URL:storeURL options:nil error:&error]) {
 
        NSLog(@"Error %@, %@", error, [error userInfo]);
        abort();
    }
    
    return _persistentStoreCoordinator;
}
 
#pragma mark - Application's Documents directory
- (NSURL *)applicationDocumentsDirectory
{
    return [[[NSFileManager defaultManager] URLsForDirectory:NSDocumentDirectory inDomains:NSUserDomainMask] lastObject];
}
 
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
}
                                                        
- (void)applicationWillResignActive:(UIApplication *)application
{
}
 
- (void)applicationDidEnterBackground:(UIApplication *)application
{
}
 
- (void)applicationWillEnterForeground:(UIApplication *)application
{
}
 
- (void)applicationDidBecomeActive:(UIApplication *)application
{
}
 
- (void)applicationWillTerminate:(UIApplication *)application
{
}
 
@end
{% endhighlight %}

Note that all of the objects are lazily initialized on first access.

* Accessor for NSManagedObjectModel

This is the easiest to create. What you need is the name of the Data Model you created in step one. You need to provide the NSURL to the Data Model file, which you create using main NSBundle. If you want to adapt that code to your application just replace ExampleModel with appropriate name.

* Accessor for NSPersistentStoreCoordinator

In our example I am using SQLite database to store our model objects. When creating the persistent store coordinator you need to provide NSURL path to the database. I use the applicationDocumentDirectory method and append the name of the file I wish the database to be stored in “ExampleDB.sqlite”. Next I initialize the coordinator with the managedObjectMode (note use of the accessor). After this persistent store can be added to the coordinator. As you can see I'm adding a NSSQLiteStoreType with the NSURL path to the database, pointer to the error and no configuration. If that operation fails youare able to retrieve error info from the error.

* Accessor for NSManagedObjectContext

Finally I'm ready to initialize the managed object context. It is very simple operation. All that is needed is the persistent store coordinator which I can assign to the managed object context.

That’s it. Now you are ready to use Core Data in your app:)
