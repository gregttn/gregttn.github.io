---
author: gregttn
comments: true
date: 2012-08-30 20:10:00+00:00
layout: post
slug: setting-text-style-of-textview-programmatically
title: Setting text style of TextView programmatically
wordpress_id: 13
categories:
- Android
---

I noticed that many people have problem with setting style of the TextView dynamically. Myself I got stuck many times in the early days trying to do that. In the ideal world you would set the text style attribute in you layout XML definition like that:


{% highlight xml %}
<TextView
	android:layout_width="wrap_content"
	android:layout_height="wrap_content"
	android:textStyle="bold"/>
{% endhighlight %}
    



There is a simple way to achieve the same result dynamically in your code by using [TextView#setTypeface](http://developer.android.com/reference/android/widget/TextView.html#setTypeface(android.graphics.Typeface)) method. You need to pass and object of [Typeface](http://developer.android.com/reference/android/graphics/Typeface.html) class, which will describe the font style for that TextView. So to achieve the same result as with the XML definition above you can do the following:

{% highlight java %}
Typeface boldTypeface = Typeface.defaultFromStyle(Typeface.BOLD);
textview.setTypeface(boldTypeface);
{% endhighlight %}

The first line will create the object form predefined style (in this case Typeface.BOLD, but there are many more predefined). Once we have an instance of typeface we can set it on the TextView. And that's it our content will be displayed on the style we defined.

This is very brief post but I hope it will help somebody.


