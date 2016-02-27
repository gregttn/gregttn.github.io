---
author: gregttn
layout: post
title: 'Reflection API in Swift 2'
---

If you have a background in languages with extensive reflection support you will notice that there is a lot missing from Swift. Reflection API in the new Swift 2 is rather basic allowing you to access a tiny amount of information.

**Note:** I used XCode Version 7.0 beta 5, you will need at least XCode 7 to run the below code.

Here is an example *class* and *struct* that later I will introspect using Reflection API.

{% highlight swift %} 
class Person {
    let name: String
    let surname: String
    let address: Address
    
    init(name: String, surname: String, address: Address) {
        self.name = name
        self.surname = surname
        self.address = address
    }
}

struct Address {
    let city: String
    let street: String
    let houseNumber: Int
}
{% endhighlight %} 

I created one *class* representing person and one *struct* for address information. These might not be the best ways to store that information but it gives me an oportunity to show you how they interact with reflection API.

Let's have a look at the reflection code.
{% highlight swift %} 
func inspect(obj: Any) {
    let type: Mirror = Mirror(reflecting:obj)
    
    print("Type of the object: \(type.subjectType)")
    print("Display style: \(type.displayStyle)")
    print("Description: \(type.description)")
    print("Number of Properties: \(type.children.count)")
    
    for child in type.children {
        print("Property: \(child.label!) Value: \(child.value) Type: \(child.value.dynamicType)")
    }
}
{% endhighlight %} 

The above code extracts pretty much all the information you can access through the reflection API. Let me explain what is happening.

* **Mirror**

Mirror is a structure that desribes the internals of a particular instance e.g. properties. You create a Mirror by passing in the object you want to know more about.

* **subjectType**

It provides information about a static type of the reflected instance.

* **displayStyle**

Used mainly by Playgrounds and debugger. It describes how the instance should be displayed.

* **description**

Textual description of the mirror

* **children**

Collection of *Child* objects. Each one describing one property of the instance. *Child* is defined as follows:

{% highlight swift %}
public typealias Child = (label: String?, value: Any)
{% endhighlight %}

*label* provides information about the property name and *value* gives you it's value. You can use *dynamicType* property on the value to access its Type.

Now that we know what the above code does, here is how to invoke it with a example *Person* object:
{% highlight swift %}
let person = Person(name: "Foo", surname: "Bar", address: Address(city: "London", street: "Awesome street", houseNumber: 10))
        
inspect(person)         
inspect(person.address)
{% endhighlight %}

Below is the output that was produced when inspect was called with *person* and *person.address* respectively:

**1.  person**

{% highlight swift %}
Type of the object: Person
Display style: Optional(Swift.Mirror.DisplayStyle.Class)
Description: Mirror for Person
Number of Properties: 3
Property: name Value: Foo Type: String
Property: surname Value: Bar Type: String
Property: address Value: Address(city: "London", street: "Awesome street", houseNumber: 10) Type: Address
{% endhighlight %}

**2. person.address**

{% highlight swift %}
Type of the object: Address
Display style: Optional(Swift.Mirror.DisplayStyle.Struct)
Description: Mirror for Address
Number of Properties: 3
Property: city Value: London Type: String
Property: street Value: Awesome street Type: String
Property: houseNumber Value: 10 Type: Int
{% endhighlight %}

As you can see, the reflection API is rather basic in comparison to other languages. In all fairness, Swift is a young language which is still under heavy development. In the future we will definately see more developments in this area.