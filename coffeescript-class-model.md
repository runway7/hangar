<!--
~~~
title: "How CoffeeScript Class Modeling Works"
slug: /javascript/coffeescript-class-model
date: 2012-01-28
publish: no
tags: [javascript, coffeescript, oop]
~~~
-->

If you haven't taken a look at [CoffeeScript](http://coffeescript.org/), do it now. 

Seriously. 

I'll wait. 

I've been looking at it for a while now and I've always dismissed it off hand as being too much work for not enough return. Javascript [suited me just fine](hangar.runway7.net/javascript-guide-to-objects-functions-scope-prototpyes-closures) for the longest time. 

But it kept coming up everywhere I looked; and some of the [libraries](http://documentcloud.github.com/underscore/docs/underscore.html) that I [like](http://spinejs.com/) a lot were [re-written](http://jashkenas.github.com/coffee-script/documentation/docs/underscore.html) in CoffeeScript. That got me curious enough to look at it. 

So now I'm a convert to the Church of Coffee, and like [great](http://brendaneich.com/2011/01/harmony-of-my-dreams/) [men](http://alexmaccaw.com/) have already [said](https://github.com/maccman/spine/issues/91#issuecomment-1783011), "I'm never going to write another line of JS if I can help it."

One of the things that intrigued me most about the language (or rather, dialect) was how the class construct would compile. I've tried to use [Backbone's](http://documentcloud.github.com/backbone/) class construct, it's practice of copying parent classes to sub classes property by property goes against the very essence of Javascript and against the principles of code reusability. 

I'm glad to say I'm quite happy with CoffeeScript, though. Here's how things work:

    :::coffeescript
    class Animal

compiles to 

    :::javascript
    var Animal;    
    Animal = (function() {    
      function Animal() {}    
      return Animal;    
    })();

Most of the code here is the safety wrapper that CoffeeScript uses around all it's code. The `Animal` declaration uses the `(function(){})()` pattern of defining a function and calling it immediately. This helps a lot in preserving scope inside of the function - almost nothing leaks out. 

    :::coffeescript
    class Animal
    
    class Dog extends Animal