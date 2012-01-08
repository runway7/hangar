<!--
~~~
title: "jQuery Deconstructed: The .each() method"
slug: /jquery-deconstructed-each-method
date: 2011-12-07
publish: no
tags: [javascript, jquery, deconstructed,jq-decon]
~~~
-->

#jQuery Deconstructed: The .each() method

*As a follow-up to the [Javascript guide][js-guide], the jQuery Deconstructed series takes a deeper look at how common jQuery code works. This is a look at the `jQuery.each()` function.*

One of the fundamental functions of jQuery is the `.each()` function. Besides being incredibly useful in a wide variety of cases, it's also used extensively inside jQuery itself. One very common case is when you call any method on a jQuery collection returned by `$`. How do you think the method is applied on all the objects in the collection?

Take a look at the full method on the [jQuery Github repo][full]. We're going to be looking at portions of it one by one. 

The method is split into four implementations, of which one is used depending on the context in which it is called. This is a common pattern in jQuery - it lets you call the same method in different ways with different kinds of arguments and chooses the best approach dynamically. 

The signature of the this method is:
    
    :::javascript
    each: function( object, callback, args ) {...}

The first branch is the check for the presence of `args`. Based on this, the authors either `call` the `callback` or `apply` the given arguments to it. (For the difference between `call` and `apply`, look here). This is because `args` needs to be an array and when calling the `callback` the only way to pass in these extra arguments is by using `apply`. 

The next branch is the check to see if the `object` is a an array (or acts like an array) or a normal 'object' (more of a dictionary). In the former (array) case, we see

    :::javascript
    for ( var i = 0; i < length; i++) {...}
    // Yes, this isn't actually what's there, but bear with me.

and in the latter (dictionary-like object) case we see 

    :::javascript 
    for (name in object) {...}



    



[full]: https://github.com/jquery/jquery/blob/255460e4836e86b86f48dd5b7d2a2cbf3f996de2/src/core.js#L607

[js-guide]: http://hangar.runway7.net/javascript-guide-to-objects-functions-scope-prototpyes-closures

