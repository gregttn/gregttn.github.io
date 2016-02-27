---
author: gregttn
comments: true
date: 2012-02-19 22:00:00+00:00
layout: post
slug: divide-space-in-linearlayout-between-components
title: Divide space in LinearLayout between components
wordpress_id: 19
categories:
- Android
---

Have you wondered how you can make components take certain part of the LinearLayout? You might have wanted to give 20% of the LinearLayout to one of the elements and then divide the rest among the rest of the children.

There is a very simple solution to that problem. By a solution I don't mean calculating exact size of the component. This is not a solution as Android applications run on different screen sizes, which makes your calculation wrong on other devices than the one on which you are testing.

Here is what we are aiming for:

{% highlight xml %}
<?xml version="1.0″encoding="utf-8″?>
<LinearLayoutxmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical">
    <TextView
        android:layout_width="fill_parent"
         android:layout_height="0dip"
        android:layout_weight="25″
        android:background="#FF0000″
        android:text="@string/one"/>
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="0dip"
        android:layout_weight="25″
        android:background="#0000FF"
        android:text="@string/two"/>
    <TextView
        android:layout_width="fill_parent"
         android:layout_height="0dip"
        android:layout_weight="25″
        android:background="#000000″
        android:text="@string/three"/>
    <TextView
        android:layout_width="fill_parent"
         android:layout_height="0dip"
        android:layout_weight="25″
        android:background="#00FF00″
        android:text="@string/four"/>
<LinearLayout>
{% endhighlight %}

As you can see in the layout file, the android:layout\_height is set to 0dip indicating that we do not know what height to assign to the element. You might be wondering why we changed android:layout\_height and not android:layout\_width. If you look again at the attributes of the LinearLayout, you'll see that the children of it will be positioned vertically. Therefore, we want to adjust the height of the element, instead of the width. In case of the LinearLayout positioning its children in horizontal fashion, we would assign android:layout\_width with 0dip value instead of android:layout\_height.

Another value that we added to each of the TextViews within LinearLayout is _android:layout_weight. _We set it to value 25 which means that each of the TextViews will take exactly 25% of the space available in the layout.

You can download the project in [here](http://gregttn.com/download/LinearLayoutVerticalWeightExample.zip)
