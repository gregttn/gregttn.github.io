---
author: gregttn
comments: true
date: 2013-01-24 13:45:00+00:00
layout: post
slug: displaying-androids-add-account-wizard-programmaticaly
title: Displaying Android's Add Account wizard programmaticaly
wordpress_id: 11
categories:
- Android
---

In one of my apps I require user to choose one of the Gmail accounts presents on the device in order to login. Once the user chose the account (and gave my app permission to access the account) I’m able to request Auth token from Google so that I can use it’s services. Recently, I realized that there was no way for the user to add account from my app. There are ways in which you can use AccountManager to add new account. I thought that there must be a way to invoke Android’s own add account activity. Not only this looks more reliable from the perspective of the user, it is also reducing the amount of code you, as a programmer, have to write.
In order to enable user to add account I created a button:

{% highlight java %}
addAccountButton = (Button) findViewById(R.id.addAccountButton);
{% endhighlight %}


My intention was to handle the responsibility of adding account over to android upon pressing the button. I added the following OnClickListener to the button:

{% highlight java %}
addAccountButton.setOnClickListener(new OnClickListener() {
	@Override
	public void onClick(View v) {
		startActivity(new Intent(Settings.ACTION_ADD_ACCOUNT));
	}
});
{% endhighlight %}


As you can see, in order to trigger Android’s add account facility all you have to do is create new Intent with Settings.ACTION\_ADD\_ACCOUNT and start activity for it. That’s it, all done. Once my application is brought back to view I update the list of accounts so that new account is displayed
