---
author: gregttn
comments: true
date: 2015-01-02 21:51:51+00:00
layout: post
slug: amazon-cognito-unauthenticated-access-ios
title: 'Amazon Cognito: unauthenticated access (iOS)'
wordpress_id: 199
categories:
- iOS
- Other
---

This is a follow up post to ["Amazon Cognito: Introduction"](/2014/11/amazon-cognito-introduction/). Please make sure you know how to work with Amazon Cognito console before continuing.

In the following paragraphs I will show you how to use Amazon Cognito service to store some values agains unauthenticated user. I will show you how to gain access to Cognito, store and retrieve data.

If you read the first article in the series you should have accountId, identityPoolId, unauthRoleArn and authRoleArn available to you. These are necessary to connect to the service.

The full source code of my sample application is available on [GitHub](https://github.com/gregttn/AmazonCognitoUnathenticated).


### Setup


Have a look at the code snippet below:

{% highlight swift %}

    class CognitoStore {
     let region: AWSRegionType = AWSRegionType.USEast1
     let poolId: String = "POOL_ID"
     let unauthArn: String = "UNAUTH_ARN"
     let authArn: String = "AUTH_ARN"
     let accountId: String = "ACCOUNT_ID"
     
     let credentialsProvider: AWSCognitoCredentialsProvider
     let client: AWSCognito
     
     init() {
       AWSLogger.defaultLogger().logLevel = AWSLogLevel.Verbose
     
       credentialsProvider = AWSCognitoCredentialsProvider.credentialsWithRegionType(region, accountId: accountId, identityPoolId: poolId, unauthRoleArn: unauthArn, authRoleArn: authArn)
     
       let configuration: AWSServiceConfiguration = AWSServiceConfiguration(region: region, credentialsProvider: credentialsProvider)
       client = AWSCognito(configuration: configuration)
       AWSServiceManager.defaultServiceManager()
        .setDefaultServiceConfiguration(configuration);
     }
    }
{% endhighlight %} 

First of all we set the log level to Verbose. This will cause the AWS to display much more information in the console, which might help you debug if you have any problems while playing with Cogntio.
There are instances of multiple classes in play here:



	
  * AWSCognitoCredentialsProvider
The name of this class is self-explanatory. In order to create it you use factory method passing it all the information that you received when creating Identity Pool like region, accountId, identityPoolId, unauthRoleArn and authRoleArn.

	
  * AWSServiceConfiguration
Instance of this class is needed to create the client. It is also used as a default configuration for other AWS services. Remember that once connected to Cognito your app can use other services provided your IAM role allows it.

	
  * AWSServiceManager
Used to set global configuration.

	
  * AWSCognito
This is our gateway to Cognito services. You need the configuration to initialise it. You will see later what can you do with this object.




### Requesting Identity


After creating the Cognito client you have to request user identifier and temporary credentials that allow your app to communicate with the AWS services. This credentials are temporary and valid for one hour.

{% highlight swift %}
    func requestIdentity() {
       credentialsProvider.getIdentityId().continueWithBlock() { (task) -> AnyObject! in
         if var error = task.error {
           println("Could not request identity: \(error.userInfo)")
         } else {
           NSNotificationCenter.defaultCenter()
            .postNotificationName(CognitoStoreReceivedIdentityIdNotification, object: self, userInfo: ["identityId":self.credentialsProvider.identityId])
         }
     
         return nil
       }
    }
{% endhighlight %} 

To request identity for your user you have to call getIdentityId() function on the credentials provider. Since call to AWS servers have to be made this method returns an instance of BFTask class from [Bolts Framework](https://github.com/BoltsFramework/Bolts-iOS). Depending on the result of the task we either print an error message or post new notification. In the example app we wait for the identity to come back so that we can access stored information and populate appropriate fields.


### Storing values


In this app we will be storing a nickname and an email of the user in the Cognito. The data is organised in data sets with key- value pairs. There are some limitations when it comes to the amount of data that can be stored. Each identity created can have up to 20 data sets with up to 1024 entries. All of that must fit in 1MB limit.

{% highlight swift %}
     func saveItem(key: String, value: String) {
       var dataSet: AWSCognitoDataset = client.openOrCreateDataset(infoKey)
       dataSet.synchronize()
       dataSet.setString(value, forKey: key)
     
       dataSet.synchronize()
     }
{% endhighlight %} 

This function takes a key and a value to be stored in our dataset. First of all, we need to open or create dataset with our specified name. Then we synchronize the data so we have a fresh copy. Finally, we set the values. Note that in this example we store string values. When the values are set only the local copy of the dataset is affected. Therefore, we call synchronize again to ensure that our data gets synced to the cloud.


### Accessing values


At this point you know how straightforward is storing information. It is equally easy to retrieve it:

{% highlight swift %}
     func loadInfo(callback: (tasks: Dictionary<NSObject, AnyObject>) -> Void) {
       var dataSet: AWSCognitoDataset = client.openOrCreateDataset(infoKey)
       dataSet.synchronize().continueWithBlock { (task) -> AnyObject! in
         if var error = task.error {
           println("Could not sync data \(error.userInfo)")
         } else {
           callback(tasks: dataSet.getAll())
         }
     
         return nil
       }
     }
{% endhighlight %} 

Here we have a method that accepts a function. This function has one argument which is Dictionary. You will shortly see why.
Similarly to the storing example we need to open or create dataset with the specified name. Next, we synchronize our copy of the dataset. You might have noticed a difference. The call to synchronize returns BFTask that I mentioned before in this post. We will wait for the task to complete and then depending on the result we will either display an error message if it failed or invoke the callback. To access all of the values of the dataset you can call getAll(). At this point we are assured that the data is synced and we get all of the entries. The call to getAll() returns a dictionary with all the values for given dataset. Here is the implementation of the callback method from the sample app:

{% highlight swift %}
     private func updateDisplayWithUserInfo(userInfo: Dictionary<NSObject, AnyObject>) {
       dispatch_sync(dispatch_get_main_queue(), { () -> Void in
         self.saveButton.enabled = true
     
         if var nick: String = userInfo[self.nickKey] as? String {
           self.nicknameField.text = nick
         }
     
         if var email: String = userInfo[self.emailKey] as? String {
           self.emailField.text = email
         }
       })
     }
{% endhighlight %} 

In here we update both of the text fields as well as the save button. The callback method might not be invoked on the main thread. To ensure that the UI elements get updated promptly without any problem I create a new block to be dispatched on the main thread. Accessing the data from the dictionary is easy given that we know what keys to look for. If the value exists we update appropriate field.


### Sample application and GitHub Repo


All code for the project is hosted in this repo. You will need CocoaPods installed.

[https://github.com/gregttn/AmazonCognitoUnathenticated](https://github.com/gregttn/AmazonCognitoUnathenticated)

Once you checkout the project from GitHub and update the Pods you will be able to run the application. Also remember to provide correct credentials in CognitoStore.swift.

[![](/assets/images/2015/01/Screen-Shot-2015-01-02-at-20.39.16.png){: .small_image}](/assets/images/2015/01/Screen-Shot-2015-01-02-at-20.39.16.png)

Provided that you got connection to the Cognito the save button will be active. You then can enter the nickname and email and save. Next time you run the app you will see these fields populated for you.

That's it for now. I intend to show you how to work with authentication at the later stage. Hope to see you then.
