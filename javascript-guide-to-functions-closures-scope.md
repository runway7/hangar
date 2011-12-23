<!--
~~~
title: "The Javascript Guide to Objects, Functions, Closures and Scope"
slug: /javascript-guide-to-objects-functions-prototpyes-closures-scope
date: 2011-12-06
publish: no
tags: [javascript]
~~~
-->

#The JavaScript guide to Objects, Functions, Prototypes, Closures and Scope

Throughout my time writing Javascript code, I've come to realize that while I love the language to bits, it is a little difficult to understand. A lot of people have attributed this to its  (admittedly not so great) design, or its obvious deviations from the paths well worn by other languages. Either way, understanding a few simple truths can go a long way with JS. 

Let's dive in with objects and variables. 

    var name = 'zaphod';
    var age = 42;

That was easy. The `var` keyword restricts the scope of the variables and prevents them from going global without us knowing, but we'll talk about that a little later. 

Now how about

	 var person = {name: 'zaphod', age: 42};

That's a little more interesting. What we're doing here is creating one variable, `person`. That variable contains the `name` and the `age` in a simple key value store… a map, if you will. Interestingly, it turns out that all Javascript objects can be thought of this way; they're all essentially key-value maps, or dictionaries. 

Accessing the data in these objects is easy:

	 alert(person.name);
	 alert(person.age);

What's more interesting is that this is equivalent to 

	 alert(person['name']);
	 alert(person['age']);

This is the first 'aha' moment for most people, because it starts to give you a hazy idea of power that this approach gives you. More on that soon, though. Let's move on. 

    var sayHello = function(){
        alert('Hello.');    
    };

There's a load of things to learn from the three lines above. First, this looks suspiciously like the way we normally define objects. *That's because it is.* Functions in JS are just glorified objects. Truth be told, they aren't even very glorified - they're just objects which can be 'called'.

    var sayHello = function(name, age) {
        return "Hello, I'm " + name + " and I'm " + age + "years old.";
    }

Simple enough. They take arguments and they return values. Better still, we could write: 

    var sayHello = function(person) {
        return "Hello, I'm " + person.name + " and I'm " + person.age + "years old.";
    }

So far so good. Call the function with your person, and you'd get what you expect. 

    sayHello(person);

So way to *call* a function *object* is to use `()`, possibly with arguments inside. 

But if functions are just objects themselves, then what's to stop us from doing this?

    person.doSomething = sayHello;

So `person.doSomething(person)` should give the same result. That's good. But the `person` seems written there too many times, though. If we're calling the function off a person, why pass the person in again? Let's try something else:

    var sayHello = function() {
        return "Hello, I'm " + this.name + " and I'm " + this.age + "years old.";
    }

So here's our first look at **scope**. Scope is the environment, or world, that the function executes in. In JS the default scope for executing functions is the `Window` object of that particular page, which is a browser level object that represents the window (or rather tab, these days) that the page is on. 

So that means running `sayHello()` (by itself) now will give you an error - `this.name` will refer to the current tab object's name, but I don't think your tab will have an `age` property. That's something to watch out for; if the function only referred to `this.name`, it would have always run, but probably not with the results you expect. 

This should give us what we want:
    
    person.doSomething = sayHello;
    person.doSomething();

Now when `doSomething` runs, it executes in the scope of `person`. So `this.name` and `this.age` will use that's person object's data. 

We still haven't modified `sayHello` itself, though. So calling `sayHello()` will still give the error. `doSomething` only has a *reference* to the same function that `sayHello` has. You can confirm this by subsequently setting `sayHello` to something else. `person.doSomething` will continue to work as expected. 

Here's another interesting piece of code:

    var zaphod = {name: 'Zaphod', age: 42};
    var marvin = {name: 'Marvin', age: 420000000000;}

    zaphod.sayHello = function(){
        return "Hi, I'm " + this.name;
    }
    marvin.sayHello = zaphod.sayHello;

So `zaphod.sayHello()` is going to give you "Hi, I'm Zaphod". Trick question: *What is `marvin.sayHello()` going to return?* 

That's the next thing to remember about scope. It's always *runtime*. This means that the scope of a function is always the context in which it *executes*, not the context in which it was *defined*. So `marvin.sayHello()` is going to return "Hi, I'm Marvin."

We already saw this in the last example: the function was actually defined in the context of `Window` when there was no obvious or explicit scope. This one makes it a lot more clear. 

Let's use what we've looked as so far to build a simple calculator. 

    var plus = function(x,y){ return x + y };
    var minus = function(x,y){ return x - y };
    // … you can add more operations if you like

    var operations = {
        '+': plus,
        '-': minus
    };





    































