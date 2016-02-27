---
author: gregttn
comments: true
date: 2012-08-08 21:42:00+00:00
layout: post
slug: customise-your-listview-in-android
title: Customise your ListView in Android
wordpress_id: 14
categories:
- Android
---

In my previous [post](/2012/07/display-items-using-listview/) I explained how to use [ListView](http://developer.android.com/reference/android/widget/ListView.html) widget using very simple example. If you have not used ListView before I would advice you to go read that post first.
Today I would like to push it a bit further and show you how to customise the look of each row within the widget. In order to do so I came up with simple scenario that will allow me to demonstrate the main concepts. Let's say you are developing an application, which will display list of people. Obviously, the first thing that comes to your mind is some kind of list widget. Furthermore, you want each row to display picture of the person, name and a button. The button will trigger simple message with date of birth of the person.

### Create PeopleListActivity

As a first step let's create our main activity called PeopleListActivity.
If you read my introduction to ListView post you should remember that main activity extended ListActivity. This time it is not the case, as I want to show you that this is not mandatory and you can extend other classes while still having list functionality.

{% highlight java %}
    public class PeopleListActivity extends Activity {
     private ListView peopleList;
    
        @Override
        public void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.main);
    
            peopleList = (ListView) findViewById(R.id.peopleList);
        }
    }
{% endhighlight %}

This listing already contains the declaration and assignment of the list widget identified by peopleList in the layout.

### Create main.xml layout

The layout file for this example is very basic. There are only two elements in there [RelativeLayout](http://developer.android.com/reference/android/widget/RelativeLayout.html) and ListView. If you are not familiar with RelativeLayout don't worry, all you need to know is that it is a layout manager, which positions it's elements relatively to each other. We will not take advantage of its functionality because we have only one element.

Here is the xml file:
    
{% highlight xml %}
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent" >
       <ListView 
           android:id="@+id/peopleList"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
      android:background="#2e2e2e"
     />
    </RelativeLayout>
{% endhighlight %}

Here we have our ListView widget. It has peopleList as it's id, which allows us to grab the view in the activity. It will also match the size of its parent, both vertically and horizontally. And finally I set the background to be #2e2e2e

### Create bean to hold data

Now we will define class, which will hold all the data we want to store for particular person. Remember that we intend to store name of the person, data of birth and a picture (avatar).
Here is a simple Person bean created just for this purpose:

{% highlight java %}
    public class Person {
     private String name;
     private Date dob;
     private int avatarResourceId;
    
     public Person(String name, Date dob, int avatarResourceId) {
      this.name = name;
      this.dob = dob;
      this.avatarResourceId = avatarResourceId;
     }
    
     public String getName() {
      return name;
     }
    
     public void setName(String name) {
      this.name = name;
     }
    
     public Date getDob() {
      return dob;
     }
    
     public void setDob(Date dob) {
      this.dob = dob;
     }
    
     public int getAvatarResourceId() {
      return avatarResourceId;
     }
    
     public void setAvatarResourceId(int avatarResourceId) {
      this.avatarResourceId = avatarResourceId;
     }
    }
{% endhighlight %}

As you noticed we are not storing any Image or Drawable as avatar. Instead we require resource id of the image to be displayed. It will be name of the file that you put in your project in res/drawable. As you will see later you can provide that by *R.drawable.imageFile*. You can read more about Drawable Resources in [here](http://developer.android.com/guide/topics/resources/drawable-resource.html).

### Create row.xml layout file

This file describes the layout of a single row within the ListView widget. In our scenario we are expected to display image, some text and a button. Here is the row.xml file we will use:
    
{% highlight xml %} 
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="horizontal" 
        android:padding="5dip">
        <ImageView
            android:id="@+id/avatar"
            android:layout_height="60dip"
            android:layout_width="0dip"
            android:layout_weight="0.2"
            android:layout_marginLeft="5dip"
            android:layout_gravity="center"
            android:contentDescription="@string/avatar"/>
        <TextView android:id="@+id/name"
            android:layout_weight="0.6"
            android:layout_height="wrap_content"
            android:layout_width="0dip"
            android:gravity="center"
            android:layout_gravity="center"/>
        <Button android:id="@+id/dobButton"
            android:layout_weight="0.2"
            android:layout_height="wrap_content"
            android:layout_width="0dip"
            android:text="@string/dob"
            android:layout_gravity="center"/>
    </LinearLayout>
{% endhighlight %}

This is fairly simple layout. As a root we have [LinearLayout](http://developer.android.com/reference/android/widget/LinearLayout.html) manager, which is set to take up all available space in the parent. It is also set to display its children in horizontal order.
First element within the layout is [ImageView](http://developer.android.com/reference/android/widget/ImageView.html). It is responsible for displaying the avatar associated with a person. We limit the height of the view to 60 dip (density independent pixels). You probably notice that the width of the image is set to 0. It is a trick that allows us to use the layout_weight property. This particular property specifies what chunk of the space should be taken by the particular view. In the case of our image view we set it to 0.2 (meaning the element will take up 20% of the parent space).
Second view is TextView, which we will use to display the name of the person. Again we use layout_weight property to set the width of the widget to 60% of the total space available to the parent container.
Last view is the actual [Button](http://developer.android.com/reference/android/widget/Button.html) that will display the date of birth of the person. It is built up from basic properties with which you should be familiar, so I will not discuss the further.

### Create custom ArrayAdapter

This is going to be the fun part of out project. In my previous post I described how to use ArrayAdapter to provide the data to the ListView. Unfortunately, It does not give you a lot of flexibility when it comes to customising the look of the row. Today we will built custom adapter by extending ArrayAdapter.

Let's start with the code:

{% highlight java %}
    public class PeopleAdapter extends ArrayAdapter{
     private LayoutInflater inflater;
     private int resource;
    
     public PeopleAdapter(Context context, int resource, int textViewResourceId, List&li;Person> people) {
      super(context, resource, textViewResourceId, people);
      this.inflater = LayoutInflater.from(context);
      this.resource = resource;
     }
    
     @Override
        public View getView(int position, View convertView, ViewGroup parent) {
      if(convertView == null) {
       convertView = inflater.inflate(resource, null);
       convertView.setBackgroundColor(Color.WHITE);
      }
    
      final Person person = getItem(position);
    
      ImageView avatar = (ImageView) convertView.findViewById(R.id.avatar);
      avatar.setImageResource(person.getAvatarResourceId());
    
      TextView name = (TextView) convertView.findViewById(R.id.name);
      name.setText(person.getName());
    
      Button dobButton = (Button) convertView.findViewById(R.id.dobButton);
      dobButton.setOnClickListener(new OnClickListener() {
       @Override
       public void onClick(View v) {
        Toast.makeText(getContext(), person.getDob().toString(), Toast.LENGTH_SHORT).show();
       }
      });
    
      return convertView;
     }
    
    }
{% endhighlight %}

Usually people recommend that you extend [BaseAdapter](http://developer.android.com/reference/android/widget/BaseAdapter.html) when trying to provide customised layout of the rows. In the code above I extended ArrayAdapter, you may ask why? In my opinion it is simpler to extend the latter as you do not have to provide implementation for many methods. When extending the ArrayAdapter you just need to override getView method in order to get the layout you desired. In case of BaseAdapter you need to provide implementation for more methods. I find it to be a waste of time to do so as ArrayAdapter already has that implementation that works as expected. There probably are people that will disagree and prefer to use BaseAdapter. At the end everything depends on your requirement. Now let's discuss the implementation.

We start with the constructor. We need to pass four parameters to our adapter:
	
  * context - the application context	
  * resource - this is the layout file id for the row
  * textViewResourceId - the id of the TextField, this is somewhat not needed as ArrayAdapter will not be automatically manipulating the content. I placed it in there to have the constructor signature match the one of ArrayAdapter
  * people - list of Person object that provide the actual data to the list view

    In constructor we also create [LayoutInflater](http://developer.android.com/reference/android/view/LayoutInflater.html) which we will use to inflate the view for each particular row.

    As I mentioned before the only method that we will need to override is the getView. This is the method that will create and populate our row layout with data. You will get three variables passed to the method. Two of them are of the most interest:

  * positon - position of the row that will be created (or reused)
  * convertView - this might be null. 

Android reuses the views once the are not visible to the user and passes them back to the getView. It improves the overall performance of ListView. When this is not null it means that the view is being reused and we do not need to recreate it.

First we check if the convertView is null. If it is we have to create view using our LayoutInflater. The only value we need to provide to it is the id of the layout resource to use.

Next step involves getting the actual Person object. It is very easy since we passed people list to the super class in our constructor. We can just call the getItem method provided by the ArrayAdapter with the position that we got passed when the getView was invoked. The person object has to be final as we will be using it later in the listener for the button.

Once we are sure that we have view to operate on we can look up the first widget that we need to provide the data for, [ImageView](http://developer.android.com/reference/android/widget/ImageView.html). We need to get the reference to the widget by calling findViewById with R.id.avatar (which is the id we set in the row.xml file for the ImageView) on the convertView. Finally, we set the image to be displayed by invoking setImageResource passing the avatarResourceId that we store on the Person object.

Second widget we need to populate is the TextView to display the name of the person. Again, we need to get the reference to the widget. Once we have it we need use the setText method of TextView to amend it's content with the name stored in the Person object.

Finally, we get the reference to the final element of our layout, the button. Earlier I said we need to display the message with the person's date of birth. We can do it by providing [OnClickListener](http://developer.android.com/reference/android/view/View.OnClickListener.html) to Button's setOnClickListener method. When creating the listener we need to provide the implementation to the onClick method since it is abstract. In that method we use [Toast](http://developer.android.com/guide/topics/ui/notifiers/toasts.html) notification to display the date of birth.

We are almost done with our little project. The last thing left, provide the data.


### Provide data

So we created the activity, Person class and PeopleAdapter. Now lets put it together:

{% highlight java %}
    public class PeopleListActivity extends Activity {
     private ListView peopleList;
    
        @Override
        public void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.main);
    
            peopleList = (ListView) findViewById(R.id.peopleList);
    
            List people = new ArrayList();
    
            Calendar cal = new GregorianCalendar();
            cal.set(Calendar.YEAR, 1980);
            people.add(new Person("John", cal.getTime(), R.drawable.happy_melon));
    
            cal.set(Calendar.YEAR, 1981);
            people.add(new Person("Greg", cal.getTime(), R.drawable.happy_apple));
    
            cal.set(Calendar.YEAR, 1982);
            people.add(new Person("Tom", new Date(), R.drawable.happy_carrot));
    
            PeopleAdapter adapter = new PeopleAdapter(this, R.layout.row, R.id.name, people);
            peopleList.setAdapter(adapter);
        }
    }
{% endhighlight %}

Simples!

### End result
So after all that let's see the end result:


[![](/assets/images/custom_list_example.png){: .small_image}](/assets/images/custom_list_example.png)










This displays the notification just after pressing the button for person. Obviously not the prettiest UI ever but you get the idea:)


**[Download on GitHub](https://github.com/gregttn/www.androify.me/tree/master/PeopleList)**
