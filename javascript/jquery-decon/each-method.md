---
title: jQuery Deconstructed: The .each() method
url: /javascript/jquery/each-method
tags: [javascript, jquery, decon]
---
*As a follow-up to the [Javascript Guide][js-guide], the jQuery Deconstructed series takes a deeper look at how common jQuery code works. I'm doing the series to make me get into the habit of reading and understanding other people's code.*

One of the fundamental functions of jQuery is `.each()`. Besides being incredibly useful in a wide variety of cases, it's also used extensively inside jQuery itself. One very common case is when you call any method on a jQuery collection returned by `$`. How do you think the method is applied on all the objects in the collection?

Take a look at the full method on the [jQuery Github repo][full]. We're going to be looking at portions of it one by one. 

The method is split into four implementations, of which one is used depending on the context in which it is called. This is a common pattern in jQuery - it lets you call the same method in different ways with different kinds of arguments and chooses the best approach dynamically. 

The signature of the this method is:
    
```javascript
  each: function( object, callback, args ) {...}
```

The first branch is the check for the presence of `args`. Based on that, the authors use either `apply` or `call` (for the difference between `call` and `apply`, look [here][call-apply]). Since `args` is an array, the only way to pass it in as arguments to the `callback` is by using `apply`. 

Either way, `args` are apparently used only internally, so we'll look at the second case that uses `call`.

The next branch is the check to see if the `object` is a an array, or acts like one, as opposed to being an object like a dictionary or function. Looks like `isObj = length === undefined || jQuery.isFunction( object )` just looks for the presence of the `length` property, so I'm guessing that not being a function and not having a `length` makes an object a plain object. 

In case it's an object, we see

```javascript 
  for (name in object) {...}
```

which is a shortcut to loop over the property keys of an object. Objects in Javascript [can all be thought of as key-value maps][js-guide], this being a handy way to iterate over the keys.

```javascript
  if ( callback.call( object[ name ], name, object[ name ] ) === false ) {
      break;
  } 
```

This `call`s the `callback` in the context of `object[name]` which is basically the corresponding value for that key. The `callback` also gets the key and the value as its parameters. 

The interesting thing here is the terseness in which the iteration break is applied: if you don't want to continue with the loop, you can return `false` from the callback for any of the elements. Doing so will `break` out of the `for` loop and stop execution. 

The array case is also very similar, except that here the index of the array takes the place of the key. 

```javascript
  for ( ; i < length; ) {
      if ( callback.call( object[ i ], i, object[ i++ ] ) === false ) {
          break;
      }
  }
```

The *intelligent* code here is the `for ( ; i < length; )`: the first clause is dropped because `i` is already declared. The last clause is dropped because `i++` is used the last time `i` is accessed in the loop. It saves an extra two characters, but I wouldn't do it given the choice. This trick works **only** if you use `i++` as opposed to `++i` and **only** if you're careful to write it **only** the last time you access `i`. Seeing 'only' that many times in one sentence means there's something wrong.

That wraps up `.each()`, making it the first in the jQuery deconstruction series. If you're curious about the inner workings of a particular jQuery method, do let me know.
    

[call-apply]: http://hangar.runway7.net/javascript-difference-between-call-apply

[full]: https://github.com/jquery/jquery/blob/255460e4836e86b86f48dd5b7d2a2cbf3f996de2/src/core.js#L607

[js-guide]: http://hangar.runway7.net/javascript-guide-to-objects-functions-scope-prototpyes-closures

