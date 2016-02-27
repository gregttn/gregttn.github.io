---
author: gregttn
comments: true
date: 2014-11-30 18:59:18+00:00
layout: post
slug: amazon-cognito-introduction
title: 'Amazon Cognito: Introduction'
wordpress_id: 178
categories:
- Android
- iOS
- Other
---

Amazon Cognito is a service aimed at developers who need quick and easy way to save and manage user data. It is very easy to use as you are not required to write complex server side code or manage any infrastructure. Amazon takes care of it all. The data you store is synced across multiple devices regardless of the operating system. In addition it also gives you functionality to create temporary and limited AWS credentials for your users so that your app can communicate with other AWS services required

Some of the functionalities include:



	
  *  identity management

	
  * no need to handle user credentials. You can use Facebook, Google, Amazon and any OpenID provider for authentication

	
  * unauthenticated access

	
  * user data storage and synchronisation

	
  * data can be written while offline and then synced to the cloud

	
  * AWS credentials provider

	
  * controlled access to other resources

	
  * push notifications


This is a first post from a series. I will walk you through creation of an identity pool. In the future posts I will show you how to use Amazon Cognito in iOS and Android apps.


### Amazon Cognito Setup


It is time for you to login to your AWS account. On the main Dashboard choose Cognito. Provided you have not worked with it before you should be greeted with screen similar to this:

[![Screen Shot 2014-11-21 at 21.43.33](/assets/images/2014/11/Screen-Shot-2014-11-21-at-21.43.33-1024x691.png)](/assets/images/2014/11/Screen-Shot-2014-11-21-at-21.43.33.png)



Click on "Get Started Now"


#### Identity Pool Creation


The main responsibility of Identity Pool is to manage user identities. You must provide unique name for your pool:

[![Screen Shot 2014-11-21 at 21.50.05](/assets/images/2014/11/Screen-Shot-2014-11-21-at-21.50.05.png)](/assets/images/2014/11/Screen-Shot-2014-11-21-at-21.50.05.png)



I think the data required should be self explanatory. If you want to use public identity providers you have to enter associated id in here. Also notice that "Enable Access to Unauthenticated Identities" checkbox is ticked. This allows access for unauthenticated users. If you are happy continue to the next step.


#### Access Management


This step allow you to create IAM roles for both authenticated users and unauthenticated users. Let's create new IAM roles with basic policies for both. It gives us enough privileges to access Cognito and store records. In a real application you would have more rules in you policy for authenticated users so that the app can access other AWS services like S3 or SNS on their behalf.

[![Screen Shot 2014-11-21 at 21.59.33](/assets/images/2014/11/Screen-Shot-2014-11-21-at-21.59.33-1024x567.png)](/assets/images/2014/11/Screen-Shot-2014-11-21-at-21.59.33.png)

Ready? Click on "Update Roles"


#### Configuration


The last screen gives you necessary information required to initialise the client. Please note your accountId, identityPoolId, unauthRoleArn and authRoleArn. You will need to these to connect to Cognito.


### Limits


Of course there are certain limits imposed by Amazon. Each identity created can have up to 20 data sets with up to 1024 entries. All of that can have up to 1MB.

This  was a simple introduction to Amamzon Cognito. It is very useful service if you aim at using AWS services for your applications. As mentioned before there will be follow up post in which I will show you how to create an application for both iOS and Android.
