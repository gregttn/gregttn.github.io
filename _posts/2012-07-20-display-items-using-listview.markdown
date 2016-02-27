---
author: gregttn
comments: true
date: 2012-07-20 23:55:00+00:00
layout: post
slug: display-items-using-listview
title: Display items using ListView
wordpress_id: 16
categories:
- Android
---

Sooner or later in your development life you will need to display items in a list format. For that purpose Android provides built in widget called [ListView](http://developer.android.com/guide/topics/ui/layout/listview.html). It requires more work than for example [TextView](http://developer.android.com/reference/android/widget/TextView.html) but in exchange gives you whole lot of control and customizations. In this post I will explain how to create the simplest ListView control that will display a list of colours.


### Create ListViewExample activity, which extends ListActivity

First of all we have to create our ListViewExample activity. In normal circumstance our new activity would extend Android's Activity, but in this case we will extend [ListActivity](http://developer.android.com/reference/android/app/ListActivity.html). ListActivity provides support for ListView out of the box. It also provides the default layout, which (surprise surprise) contains only ListView widget. Therefore, you do not need to explicitly set a layout or create layout XML file. You will need to do that if you want to customise the look of the rows, which we will discuss in later article.

Furthermore, there are several events handlers that are also provided.

Here is the implementation of the ListViewExample:

{% highlight java %}
public class ListViewExample extends ListActivity {    
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
    }
}
{% endhighlight %}

### Create ArrayAdapter

So we have our activity created, right? But we need some data to display. ListView accepts so called Adapter that provides the data to be displayed in the rows. Android's documentation describes it as a bridge between data and the ListView. With regards to our problem we need adapter that implements the [ListAdapter](http://developer.android.com/reference/android/widget/ListAdapter.html). There are implementation of this interface like [ArrayAdapter](http://developer.android.com/reference/android/widget/ArrayAdapter.html), [SimpleCursorAdapter](http://developer.android.com/reference/android/support/v4/widget/SimpleCursorAdapter.html), [CursorAdapter](http://developer.android.com/reference/android/support/v4/widget/CursorAdapter.html) and others. In this particular example we will deal with ArrayAdapter. It is very simple to use. All you have to do is to provide the data in a form of String array and resource id where to display the text. This resource id is the default id provided by android itself. As you remember I mentioned that ListActivity already has the default layout. In this layout the id of the text view is

*android.R.layout.simple\_list\_item*

Here is how we create the adapter:

{% highlight java %}
    String[] colors = {"Red","Green","Blue","Yellow","Violet"};
    ArrayAdapter adapter = new ArrayAdapter(this, android.R.layout.simple_list_item_1, colors);
{% endhighlight %}

### Connect the ArrayAdapter to the ListView

Finally, we have to set the adapter to the ListView using *setListAdapter(adapter)* method on the activity.

Here is how all code should look like now.

{% highlight java %}
    public class ListViewExample extends ListActivity {
    
        @Override
        public void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
    
            String[] colors = {"Red","Green","Blue","Yellow","Violet"};
            ArrayAdapter adapter = new ArrayAdapter(this, android.R.layout.simple_list_item_1, colors);
            super.setListAdapter(adapter);
        }
    }
{% endhighlight %}

### End Result

[![](/assets/images/list_example.png){: .small_image}](/assets/images/list_example.png)

I will describe further customisation you can make to the ListView widget in future posts.

Hope this post helps somebody:)
