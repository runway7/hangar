<!--
~~~
title: The Unchangeables: Immutability and Value Objects
slug: /immutability-and-value-objects
date: 2011-12-07
publish: no
tags: [programming, punditry]
~~~
-->

#The Unchangeables: Immutability and Value Objects

This post was born out of a few of the training programs that I've conducted in the last couple of years. It turns out that a lot of programmers, both fresh graduates and those with a lot of experience, just don't think about [immutability][im] and [value][vo1] [objects][vo2]. 

This is really sad because applying these ideas to code uniformly produces better design, extensibility and readability. Both concepts are fairly complementary - I think of them as two sides of same coin. And speaking of coins, they're a great example to start with. 

### Value Objects 

If you pick up a coin or a note, you know instinctively that you'd trade it for any other coin (or note) of the same value. You don't know the serial numbers of all the notes in your wallet, nor do you care. Both coins and notes are examples of value objects: what they mean to you is dependent solely on the value they hold, and they're completely interchangeable when they have the same value. That is the crux of the value object idea: the objects have no identity of their own, except that which they derive from their value(s). 

But although we understand this in theory in the real world, we rarely use it in our code. A lot of the classes we create can and probably should be written as value objects, instead they wind up as examples of what not to do in blog posts like this. 

One example that I exercise that I use often is length conversion:

> Accept lengths in either meters, centimeters or millimeters, and provide ways to compare and convert freely between them. 

When people try this in Java, here's some of the code I commonly see:

    :::java
    Meter l1 = new Meter(1);
    Centimeter l2 = Units.convertMeterToCentimeter(l1);
    Millimeter l3 = Units.convertCentimeterToMillimeter(l2);
    Meter l4 = Units.convertMillimeterToMeter(l3);
    assertEquals(l1, l4);

While this would technically get the job done, the API here is horrendous. The number of static conversion methods will quickly explode as we add more types of units, there's isn't much type safety (I can't just take a `Length` without caring what unit it is) and it just looks really ugly. 

With a little thought and skill, this can quickly be re-written to

    :::java
    Length l1 = new Meter(1);
    Length l2 = new Centimeter(10)
    assertEquals(l1, l2);
    new Millimeter(l1).toString() //-> 1000mm 

By simply recognizing that all of the possible objects in this exercise are simply value objects with a length value, we can come up with a design that's far more extendable, readable and elegant… not to mention useful. 

The fundamental premise here is that all objects representing length (of any unit) are fundamentally holding one value - a distance. That's all that matters. Everything else is just cruft. 

As soon as we realize that, we can just write code to store one value in a standardized unit in a `Length` abstract class, and have all conversions happen in the unit subclasses. 

###Immutability



    
   



[vo1]: http://domaindrivendesign.org/node/135
[vo2]: http://devlicio.us/blogs/casey/archive/2009/02/13/ddd-entities-and-value-objects.aspx
[im]: http://en.wikipedia.org/wiki/Immutable_object

