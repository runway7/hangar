<!--
~~~
title: "The Javascript Guide to Objects, Functions, Closures and Scope"
slug: /javascript-guide-to-objects-functions-scope-prototpyes-closures
date: 2011-12-26
publish: yes
tags: [javascript, guides]
~~~
-->

#The JavaScript guide to Objects, Functions, Scope, Prototypes and Closures

Throughout my time writing Javascript code, I've come to realize that while I love the language to bits, it is a little difficult to understand. A lot of people have attributed this to its  (admittedly not so great) design, or its obvious deviations from the paths well worn by other languages. Either way, understanding a few simple truths can go a long way with JS. What follows is the condensed and written version of the introductory class on JavaScript that I give at training programs and user groups.

Let's dive in with objects and variables. 

    :::javascript
    var name = 'zaphod';
    var age = 42;

That was easy. The `var` keyword restricts the scope of the variables and prevents them from going global without us knowing, but we'll talk about that a little later. 

Now how about

    :::javascript
	 var person = {name: 'zaphod', age: 42};

That's a little more interesting. What we're doing here is creating one variable, `person`. That variable contains the `name` and the `age` in a simple key value store… a map, if you will. Interestingly, it turns out that all Javascript objects can be thought of this way; they're all essentially key-value maps, or dictionaries. 

Accessing the data in these objects is easy:

    :::javascript
	 alert(person.name);
	 alert(person.age);

What's more interesting is that this is equivalent to 

    :::javascript
	 alert(person['name']);
	 alert(person['age']);

This is the first 'aha' moment for most people, because it starts to give you a hazy idea of power that this approach gives you. More on that soon, though. Let's move on. 

    :::javascript
    var sayHello = function(){
        alert('Hello.');    
    };

There's a load of things to learn from the three lines above. First, this looks suspiciously like the way we normally define objects. *That's because it is.* Functions in JS are just glorified objects. Truth be told, they aren't even very glorified - they're just objects which can be 'called'.

    :::javascript
    var sayHello = function(name, age) {
        return "Hello, I'm " + name + " and I'm " + age + "years old.";
    }

Simple enough. They take arguments and they return values. Better still, we could write: 

    :::javascript
    var sayHello = function(person) {
        return "Hello, I'm " + person.name + " and I'm " + person.age + "years old.";
    }

So far so good. Call the function with your person, and you'd get what you expect. 

    :::javascript
    sayHello(person);

So way to *call* a function *object* is to use `()`, possibly with arguments inside. 

But if functions are just objects themselves, then what's to stop us from doing this?

    :::javascript
    person.doSomething = sayHello;

So `person.doSomething(person)` should give the same result. That's good. But the `person` seems written there too many times, though. If we're calling the function off a person, why pass the person in again? Let's try something else:

    :::javascript
    var sayHello = function() {
        return "Hello, I'm " + this.name + " and I'm " + this.age + "years old.";
    }

So here's our first look at **scope**. Scope is the environment, or world, that the function executes in. In JS the default scope for executing functions is the `Window` object of that particular page, which is a browser level object that represents the window (or rather tab, these days) that the page is on. 

So that means running `sayHello()` (by itself) now will give you an error - `this.name` will refer to the current tab object's name, but I don't think your tab will have an `age` property. That's something to watch out for; if the function only referred to `this.name`, it would have always run, but probably not with the results you expect. 

This should give us what we want:
    
    :::javascript
    person.doSomething = sayHello;
    person.doSomething();

Now when `doSomething` runs, it executes in the scope of `person`. So `this.name` and `this.age` will use the data in the `person` object. To be absolutely explicit, you can always write `sayHello.call(person);` which will run `sayHello` in the context of `person`. The `call` method is very useful in scope related code.

We still haven't modified `sayHello` itself, though. So calling `sayHello()` will still give the error. `doSomething` only has a *reference* to the same function that `sayHello` has. You can confirm this by subsequently setting `sayHello` to something else. `person.doSomething` will continue to work as expected. 

Here's another interesting piece of code:

    :::javascript
    var zaphod = {name: 'Zaphod', age: 42};
    var marvin = {name: 'Marvin', age: 420000000000;}

    zaphod.sayHello = function(){
        return "Hi, I'm " + this.name;
    }
    marvin.sayHello = zaphod.sayHello;

So `zaphod.sayHello()` is going to give you "Hi, I'm Zaphod". Trick question: *What is `marvin.sayHello()` going to return?* 

That's the next thing to remember about scope. It's always *runtime*. This means that the scope of a function is always the context in which it *executes*, not the context in which it was *defined*. So `marvin.sayHello()` is going to return "Hi, I'm Marvin."

We already saw this in the last example: the function was actually defined in the context of `Window` when there was no obvious or explicit scope. This one makes it a lot more clear. 

Let's use what we've looked at so far to build a simple calculator. 

    :::javascript
    var plus = function(x,y){ return x + y };
    var minus = function(x,y){ return x - y };

    var operations = {
        '+': plus,
        '-': minus
    };

The functional way to use this would probalby be someting like this:

    :::javascript
    var calculate = function(x, y, operation){
        return operations[operation](x, y);
    }
    
    calculate(38, 4, '+');
    calculate(47, 3, '-');
    
This example also shows how absurdly easy it is to implement some patterns like the [strategy pattern](http://en.wikipedia.org/wiki/Strategy_pattern) in JavaScript. While I've never liked implementing patterns for their own sake, this is still something to remember. 

On the other hand, let's try something a little more object-oriented. While most 'object oriented' langauges today use class based inheritance, JavaScript uses prototypes. There is no class construct here, but it's still a very powerful langauage. Let's see how things work:

    :::javascript
    var Problem = function(x, y){
        this.x = x;
        this.y = y;        
    }

    var problem1 = new Problem(4, 5);
    
So here we have a function that takes two parameters, `x` and `y` and stores them. The `new` keyword is essential here because it tells the interpreter that you're using `Problem` not as a function, but as a constructor instead. Leaving it out will simply execute the `Problem` function, which returns nothing, so `problem1` will have no value. The `new` keyword gives us a copy of the function object after running it, while preserving the original intact (so we can use it to make more `problem` objects).

Remember that `Problem` itself is a perfectly normal function - we only capitalize the 'P' as a convention to remind ourselves to use the `new` keyword, and all the constructor behavior comes from the use of the `new` keyword. 

Also keep in mind that since functions are simply objects, we can easily do this:

    :::javascript
    alert(problem1.x);
    alert(problem1.y);

    
Now we'd like to have our `Problem` capable of solving itself, so let's do this:

    :::javascript
    Problem.prototype.operations = {
        '+': function(x,y){ return x + y; },
        '-': function(x,y){ return x - y; }
    };
    
    Problem.prototype.calculate = function(operation){
        return this.operations[operation](this.x, this.y);
    };
    
    problem1.calculate('+');
    problem1.calculate('-');
    
So here's our first look at the JavaScript **prototype**. Although prototypes are one of the cornerstones of the language, it's possible to write JS for years and not come across any reason to use them. Like most things, though, understanding them is essential to mastery. 

In JS, each and every object has one built-in property called the `__proto__`. This property refers to another object which is considered the prototype for this object, a sort of *parent* or *mould* or *original* from which this object was created. This is a very useful link because it means that all objects have the properties and methods of their prototype. 

Conversely, modifying a prototype to add new features to it will result in that new feature being available to all the objects which have that prototype. 

The prototype model in JS works similar to the class model in other languages, in the sense that when a method is not found in the class definition of an object, it's superclass is then checked, then the superclass' superclass, and so on. The same process takes place here - first the object is checked. If the method or property is not found, it's `__proto__` is checked, then it's prototype's `__proto__` and so on, until the interpreter hits the last object on the chain (`__proto__` is `null`).

You'll notice though, that we haven't touched `__proto__` anywhere in our code. That's because it doesn't make sense to and is a *very bad idea* to do so. Instead JS lets you use the `prototype` object on the function that you're using as a constructor (`Problem`, in this case) to specify the behavior of the prototypes of the constructed objects. It automatically sets the `__proto__` of the constructed objects to `Problem.prototype` for you. 

Now it's easy to see that calling `problem1.calculate()` actually looks at `problem1` itself first. Finding no `calculate` method, it then looks at its `__proto__`, which is the same as `Problem.prototype`, on which it finds the method. It then runs `calculate` in the scope of `problem1`. This is how the method can be defined only in the prototype and still work correctly in all the objects which have that prototype. 

We also notice that this prototype resolution (like most things in JavaScript) is *runtime*. We're adding methods to `Problem.prototype` *after* creating the `problem1` and `problem2` objects, but we still have no issues using these methods on `problem1` and `problem2`. The interpreter resolves the function calls at the times at which you actually call them. 

Let's add one final method to our `Problem`:

    :::javascript
    Problem.prototype.newMessageMaker = function(){
        var self = this;
        var formatter = function(x, y){
            return 'Values: ' + self.x + ' and ' + self.y;
        };

        return function(start, end){
            return '' + start + formatter() + end;
        };
    }

    var messageMaker = problem1.newMessageMaker();
    // Do lots of things.
    var message = messageMaker('This was the problem: ', 'Thanks for solving it!');
    alert(message);


Now this is a contrived example, but it serves to illustrate a very important part of JavaScript - the concept of **closures**. Here, the `problem1.newMessageMaker()` actually returns *another function*, which you can then call at your leisure. 

The interesting bit here is the `formatter` function. We know it's being defined in the `newMessageMaker` function, but `newMessageMaker` executes and finishes long before `messageMaker` is actually used. What then happens to `formatter`? Isn't it supposed to have vanished?

The answer is that the function returned by `newMessageMaker` *closes over* the `formatter` variable. It holds on the value of `formatter` long after everything else lets go, including the function that originally defined it. 

This is also how the trick with the `self` variable works. We can't really use `this` in this case because we have no idea what scope the `messageMaker` is going to run in. So we assign `this` to some variable (here we use `self`) and use it in the function that we're going to give away. That way, the closure will ensure that the right data is always used, irrespective of which scope the function eventually runs in. 

Closures are especially useful in AJAX code. More often than not, you'll find yourself passing a function to the AJAX library to run after a particular server call is finished. If you need that function to have access to any special data that you don't want to give the library, closures are a great way to do it. 

This is a fairly small start down the road to JavaScript mastery and there's still a long way to go, but, as always, actually trying things out and reading other people's good code is a great way to move forward. 




























