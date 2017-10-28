---
title: Quick and Easy Javascript Namespacing
url: /javascript/namespacing
tags: [javascript]
published: true
---

One of the ways to stop polluting the global environment in  Javascript is namespacing - holding all your objects and data in a hierarchy  inside of one variable. 

Let's say you want to write a method in `Foods.Grains.Wheat`, without worrying about `Foods` and `Foods.Grains`. Maybe they're in different files, being worked on by different teams, or whatever. While there are many ways to do this, here's one common way: 

```javascript
window.Foods = window.Foods || {};
Foods.Grains = Foods.Grains || {};
Foods.Grains.Wheat = Foods.Grains.Wheat || {};

Foods.Grains.Wheat.harvest = function(){
  // Do something
}
```

While this method gets the work done - you get to use the namespace without overwriting code from other files - it results in a lot of unnecessary code at the top of each file. You're essentially ensuring the existence of each namespace that you want to use, and doing it again at the top of each file. 

There's an easier way, though:
    
```javascript
var ns = function(namespace){
  return namespace.split('.').reduce(function(holder, name){
    holder[name] = holder[name] || {};
    return holder[name];
  }, window);
};
```

Make sure that's included first, then do 

```javascript
  ns('Foods.Grains.Wheat');
```

at the top of each file that you use it in. 

The code itself is fairly simple if you're into functional programming and have used map / reduce before. The `namespace.split('.')` breaks the namespace into it's constituent parts. `reduce` then steps through each part from left to right, ensuring that it exists as an object on `holder`, and returning the object that does. The process starts with `window`.

If you're not familiar with functional programming, hopefully there'll be a post soon with some examples (at least for `map` and `reduce`). [Subscribe](http://feeds.feedburner.com/hangar-runway7) if you don't want to miss it.

It normally helps to add the `ns` to jQuery (or to any other library) as a plugin. Also, since `reduce` as a built-in function is fairly new, not all browsers support it. Feel free to replace it with a [library-provided method](http://documentcloud.github.com/underscore/#reduce), or add [this implementation](http://www.tutorialspoint.com/javascript/array_reduce.htm) in. If you're using NodeJS, you'll definitely want to replace `window`, probably in [an environment-agnostic way](https://github.com/documentcloud/underscore/blob/9c88b723629b8a225f79ca788d92536b1ce04e1d/underscore.js#L51).


