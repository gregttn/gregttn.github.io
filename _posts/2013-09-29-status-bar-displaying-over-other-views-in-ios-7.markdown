---
author: gregttn
comments: true
date: 2013-09-29 20:21:01+00:00
layout: post
slug: status-bar-displaying-over-other-views-in-ios-7
title: Status bar displaying over other views in iOS 7
wordpress_id: 71
categories:
- iOS
---

Following the release of iOS7  I updated my iPhone to iOS7 as well as XCode to version 5 so that I can check what has changed and what is new. For the past couple of weeks I've been working on an app that seemed to have subtle problems under the new system.

One of the issues that annoyed me the most (regardless of how easy it is to fix) was the status bar overlaying on top of my views in one of my view controllers:




[![2013-09-29 20.41.14](/assets/images/2013/09/2013-09-29-20.41.14.png){: .small_image}](/assets/images/2013/09/2013-09-29-20.41.14.png)







As you can see the status bar overlays the Cancel and Save button. I did a bit of googling around and found a solution that worked for me. It appears that this issue can be fixed by adding the following method to the controller:




**-(BOOL)prefersStatusBarHidden { return YES; }**




This methods allows you to hide the status bar regardless of other settings you may have. This solved my problem as I needed the status bar space in that controller anyway.




[![2013-09-29 20.40.51](/assets/images/2013/09/2013-09-29-20.40.51.png){: .small_image}](/assets/images/2013/09/2013-09-29-20.40.51.png)






