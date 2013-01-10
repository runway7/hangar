If you haven't taken a look at [CoffeeScript](http://coffeescript.org/), do it now. 

Seriously. 

I'll wait. 

I've been looking at it for a while now and I've always dismissed it off hand as being too much work for not enough return. Javascript [suited me just fine][guide] for the longest time. 

But it kept coming up everywhere I looked; and some of the [libraries](http://documentcloud.github.com/underscore/docs/underscore.html) that I [like](http://spinejs.com/) a lot were [re-written](http://jashkenas.github.com/coffee-script/documentation/docs/underscore.html) in CoffeeScript. That got me curious enough to look at it. 

So now I'm a convert to the Church of Coffee, and like [great](http://brendaneich.com/2011/01/harmony-of-my-dreams/) [men](http://alexmaccaw.com/) have already [said](https://github.com/maccman/spine/issues/91#issuecomment-1783011), "I'm never going to write another line of JS if I can help it."

One of the things that intrigued me most about the language (or rather, dialect) was how the class construct would compile. <del>I've tried to use [Backbone's](http://documentcloud.github.com/backbone/) class construct, but its practice of copying parent classes to sub classes property by property goes against the very essence of Javascript and against the principles of code reusability. I think the latest version has moved to proper prototypal inheritance, but that's what got me thinking about this in the first place.</del>

**Edit**: *Jeremy Ashkenas mailed me to point out that this simply wasn't true - I'm chalking it down to my own ignorance at that time. I don't think I understood prototypes at all then.*

I'm glad to say I'm quite happy with CoffeeScript, though. Here's how things work:

```coffeescript
  class Animal
```

compiles to 

```javascript
var Animal;
Animal = (function() {    
  function Animal() {}
  return Animal;
})();
```

Most of the code here is the safety wrapper that CoffeeScript uses around all it's code. The `Animal` declaration uses the `(function(){})()` pattern of defining a function and calling it immediately. This helps a lot in preserving scope inside of the function -  nothing leaks out, except the variables you intentionally export. Other than that, it's [fairly standard][guide] Javascript class creation technique. 

So let's try

```coffeescript
class Animal

class Dog extends Animal
```

which explodes to:

```javascript
var Animal, Dog,
  __hasProp = Object.prototype.hasOwnProperty,
  __extends = function(child, parent) { for (var key in parent) { if (__hasProp.call(parent, key)) child[key] = parent[key]; } function ctor() { this.constructor = child; } ctor.prototype = parent.prototype; child.prototype = new ctor; child.__super__ = parent.prototype; return child; };

Animal = (function() {    
  function Animal() {}
  return Animal;
})();

Dog = (function(_super) {    
  __extends(Dog, _super);    

  function Dog() {
    Dog.__super__.constructor.apply(this, arguments);
  }

  return Dog;

})(Animal);
```

which, of course, is where all the fun stuff is. The core is the `__extends` method (I've added line breaks):

```javascript
__extends = function(child, parent) { 
  for (var key in parent) { 
    if (__hasProp.call(parent, key)) child[key] = parent[key]; 
  } 
  function ctor() { 
    this.constructor = child; 
  } 
  ctor.prototype = parent.prototype; 
  child.prototype = new ctor; 
  child.__super__ = parent.prototype; 
  return child; 
};
```

The `for` loop simply copies any properties you might have set on the parent to the child. This might seem odd at first, because we want inheritance, not cloning. But keep in mind that these are the properties being set on the 'class' itself. That doesn't include the methods for `Animal` - they would have been set on `Animal.prototype`, and the object level data would have been set on the individual objects created using `new Animal()` (see the [Guide][guide] for more information). It just copies over class level, or 'static' variables. 

```javascript
function ctor() { 
  this.constructor = child; 
} 
ctor.prototype = parent.prototype; 
child.prototype = new ctor; 
child.__super__ = parent.prototype; 
return child; 
```

These lines of code exist to keep one of the promises of CoffeeScript - that all classes will have a `constructor` method. When `__extends(Dog, Animal)` is called, it creates a sort of *in-between* class which has the `constructor` method as the function used to define `Dog`. An instance of this class is then set as `Dog`'s prototype, so sub-classes of `Dog` will be able to refer to the `constructor`. The link with `Animal` is established by setting the prototype of `ctor` to `Animal`. 

The final step is to assign `__super__` on `Dog` to `Animal`'s prototype, which is a quick short-cut to access the parent class while by-passing the `ctor` construct in between. This is also what `super` compiles to. 

This all seems like a very convoluted way to create a class construct, but the upside is that the end result is actually very intuitive and does what you expect. The compiled JavaScript is also fully interoperable with normal hand-written code, which I think is quite awesome. I've learnt a lot of stuff just looking at these few lines. 


[guide]: http://hangar.runway7.net/javascript-guide-to-objects-functions-scope-prototpyes-closures