---
layout: post
title: Objects in Javascript and Ruby
author: Lucas Moore
---

Now that I've completed the Learn Verified curriculum, I'm going back and reviewing some basic elements of the languages I've learned. Ruby and Javascript, have many similarities, but also have their own unique style.

One of the most important steps of [accelerated learning](http://dev.thelucasmoore.com/2016/03/17/How-I-Have-Learned-To-Learn.html) is to review content at regular intervals, moving it from short term to long term memory. So here's a simple explanation of object-oriented practices in Ruby and Javascript. 

## A Common Vocabulary

When writing object oriented programs, there is a common vocabulary to any language. Here's the highlights: 

1. *Class* - a definition of an object's characteristics, including properties and methods.
2. *Method* - an action done by an object.
3. *Property* - a characteristic of an object (like color or name).
4. *Object* - a specific instance of a class, which behaves like an object and has methods and properties. 
5. *Constructor* - the method called to build a new object.

## Ruby Objects

Everything in Ruby is an object. If we want to create a person, we can create a Person class and instantiate a person object quite simply.

```ruby
class Person
end

> person1 = Person.new
#  => #<Person:0x007fbb419c8fe8> 
> person2 = Person.new
#  => #<Person:0x007fbb41991520> 
```

Now we have two person objects built by using the <code>Person.new</code> constructor, but they don't have any properties or methods associated with them. That's quite boring. Let's give these people names and teach them how to dance. 

```ruby
class Person
  def initialize(name)
    @name = name
  end

  def dance
    puts "SHAKIN' IT!"
  end
end

> steve = Person.new("Steve")
#  => #<Person:0x007fbb4182e3b8 @name="Steve">
> steve.dance
#  => "SHAKIN' IT!"
```
Much better. Now, let's do the same thing in Javascript.

## Javascript Objects

In javascript, there are no class constructors. Rather, regular old functions construct the objects, which are built with a capital letter.

```javascript
var Person = function() {
};

var person1 = new Person();
var person2 = new Person();
```
Variables are defined with <code>var</code> and the <code>new Person()</code> constructor creates the instance of those objects. Again, there's nothing special about these people yet, so let's give them names.

```javascript
var Person = function(name) {
  this.name = name;
}

var steve = new Person('Steve');
// steve.name = 'Steve'
```

To teach these objects new methods, like how to dance, we can add a method into the object prototype. Here, we add this function to the associated Person object.

```javascript
// ...to add to the above code

Person.prototype.dance = function() {
  console.log(this.name + "is getting funky!");
}

steve.dance // logs "Steve is getting funky!"
```

