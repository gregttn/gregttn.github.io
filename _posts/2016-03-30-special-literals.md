---
author: gregttn
comments: true
date: 2016-03-30
layout: post
slug: Special literals in Swift 2.2
title: Special literals in Swift 2.2
categories:
- iOS
---
As every developer I'm constantly learning and exploring new things. This is particularly true with Swift and iOS since it is not the main language/platform I work with on a daily basis. Recently I stumbled upon special literals that allow you to grab information that might be of interest to you the developer. They caught my attention because they can provide useful information that might be needed when there is an issue with your application.

### Available Macros

In this post I will focus on these four macros:

* **#file** (String) 
    
  Provides the name of the file in which it is located

* **#function** (String)
    
  Provides different info depending where it is placed. By default it provides the name of the function. If it is located in *init* or *subscript* it will provide that keyword instead. If you place this literal at the beginning of the file you will get a name of the current module.

* **#line** (Int) 
    
  Provides the line number. If this token is used on its own line it has a different meaning. In that case it is regarded as [Line Control Statement](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Statements.html#//apple_ref/doc/uid/TP40014097-CH33-ID540)

* **#column** (Int) 
    
  Provides the location on the line

^

### Example usage

As I mentioned at the beginning you might find them particulary useful when you need to report on exception that happened in your code. You can capture information that will be helpful in finding out where things fell apart.

Imagine we have the following type that represents errors that our application might throw:


```swift
public enum SomeError: ErrorType {
    case Foo
    case Bar
}
```

For the purpose of this post let's also create a simple function that always throws an exception:

```swift
func willThrowException() throws {
    throw SomeError.Foo
}
```

Now we can proceed and use the unfortunate method:

```swift
func doSomething() {
    do {
        try willThrowException()
    } catch {
        print("Horrible error caught!")
    }
}
```

I am nice here and catch the exception that might be thrown. When this happens an error message is printed that says "Horrible error caught!". In our case it is a simple error message which would be quite easy to locate. However, in larger applications that might not be the case. Ideally, we would like to have some more information. Thanks to our special literals we can create a logger like this:

```swift
public class Logger {
    static func log(msg: String, function: String = #function, file: String = #file, line: Int = #line, column: Int = #column) {
        print("Message: \(msg)")
        print("File: \(file); func: \(function); Line: \(line); Column: \(column)")
    }
}
```

As you can see the log method has a mandatory message parameter. In addition there are four more parameters which use the macros to provide default value. This *Logger* class can now be used in our *doSomething()* method:

```swift
func doSomething() {
    do {
        try willThrowException()
    } catch {
        Logger.log("Horrible error caught!")
    }
}
```

Running this produces the following output on my Swift 2.2 Ubuntu build:

[![](/assets/images/2016/error_output.png)](/assets/images/2016/error_output.png)

That's better! You can now track the line that reported the error.

### Before Swift 2.2

There was a slightly different syntax for these literals before Swift 2.2. The old syntax used underscores instead of the hash sign. The old versions of the macros looked like this: 

* **\_\_FILE\_\_**
* **\_\_FUNCTION\_\_**
* **\_\_LINE\_\_**
* **\_\_COLUMN\_\_**

^

### Code

The code for *ThrowException.swift* is available here:

[https://gist.github.com/gregttn/b6a6cfdda4715828e6ad31befb21fa01](https://gist.github.com/gregttn/b6a6cfdda4715828e6ad31befb21fa01)

You can run it without problems on Mac OS X if you have the new version of swift installed. Alternatively, you can have a look at my Dockerfile I use to create image with Swift 2.2 running on Ubuntu 15.10:

[https://github.com/gregttn/DockerImages/tree/master/swift2.2](https://github.com/gregttn/DockerImages/tree/master/swift2.2)



