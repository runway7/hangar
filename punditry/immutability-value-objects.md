
This post was born out of the discussions and sessions in training programs that I've conducted in the last couple of years. It turns out that a lot of programmers, both fresh graduates and those with a lot of experience, just don't think about [immutability][im] and [value][vo1] [objects][vo2]. 

This is really sad because applying these ideas to code uniformly produces better design, extensibility and readability. Both concepts are fairly complementary - I think of them as two sides of same coin. And speaking of coins, they're a great example to start with. 

### Value Objects 

If you pick up a coin or a note, you know instinctively that you'd trade it for any other coin (or note) of the same value. You don't know the serial numbers of all the notes in your wallet, nor do you care. Both coins and notes are examples of value objects: what they mean to you is dependent solely on the value they hold, and they're completely interchangeable when they have the same value. That is the crux of the value object idea: the objects have no identity of their own, except that which they derive from their value(s). 

But although we understand this in theory instinctively in the real world, we rarely use it in our code. A lot of the classes we create can and probably should be written as value objects, instead they wind up as examples of what not to do in blog posts like this. 

One example that I exercise that I use often is a length conversion problem:

> Accept lengths in either meters, centimeters or millimeters, and provide ways to compare and convert freely between them. 

When people try this in Java, here's some of the code I commonly see:

```java
Meter lengthOne = new Meter(1);
Centimeter lengthTwo = Units.convertMeterToCentimeter(lengthOne);
Millimeter lengthThree = Units.convertCentimeterToMillimeter(lengthTwo);
Meter lengthFour = Units.convertMillimeterToMeter(lengthThree);
assertEquals(lengthOne, lengthFour);
```

While this would technically get the job done, the API here is horrendous. The number of static conversion methods will quickly explode as we add more types of units, there's isn't much type safety (I can't just accept a `Length` without caring what unit it is) and it just looks really ugly. 

With a little thought and skill, this can quickly be re-written to

```java
Length oneMeter = new Meter(1);
Length tenCentimeters = new Centimeter(10)
assertEquals(oneMeter, tenCentimeters);
new Millimeter(oneMeter).toString() //-> 1000mm 
```

The foundational premise here is that all objects representing length (of any unit) are basically just holding one value - a distance. That's all that matters. Everything else is just cruft. 

As soon as we realize that, we can just write code to store one value in a standardized unit in a `Length` abstract class, and have all conversions happen in the unit subclasses. 

By simply recognizing that all of the possible objects in this exercise are simply value objects, we can come up with a design that's far more extendable, readable and elegant… not to mention useful. 


###Immutability

The other side of the metaphorical coin is immutability. While immutability and value objects don't necessary need to go hand in hand, I find that they often do, and bring mutual benefits. 

Immutability is basically permanence. Once you have a coin in your hands, there simply isn't any way that you can change it's value. The only thing you could possibly do is destroy it and make a new coin, which is actually very representative of immutable objects. 

Applying the same idea to objects gives you a load of benefits. The first and most important of them is that the design of the code drastically improves - gone are all your setters, and maybe even your getters, and any other code that exists to modify the state of the object. You automatically start in think in a more functional style, since the number of possible [side effects][side-effects] that your methods can have comes down significantly. 

The second big win comes in the reduction of bugs relating to mutation and thread safety: all immutable objects are inherently thread-safe, and do not suffer from the bugs that occur when objects are unexpectedly modified in a different part of the code. 

Immutability also helps secure your code against other code (either someone else's or your own): if a piece of code relies on a object and passes it to another method, there's simply no way the other method can jeopardize things by modifying the object. One way this security is commonly achieved is by only passing copies of the object out, but if the object is immutable, that overhead and complexity can easily be avoided. 

The implementing both value objects and immutability is more enforceable when using static languages like Java and C#, where constructs like Java's `private` and `final` keyword come in handy. Even in dynamic languages like Ruby and Python, it's still possible, though [slightly][ruby-accessors] more [roundabout][python-property]. 



[vo1]: http://domaindrivendesign.org/node/135
[vo2]: http://devlicio.us/blogs/casey/archive/2009/02/13/ddd-entities-and-value-objects.aspx
[im]: http://en.wikipedia.org/wiki/Immutable_object
[side-effects]: http://en.wikipedia.org/wiki/Side_effect_(computer_science)
[ruby-accessors]: http://www.rubyist.net/~slagell/ruby/accessors.html
[python-property]: http://docs.python.org/library/functions.html#property
