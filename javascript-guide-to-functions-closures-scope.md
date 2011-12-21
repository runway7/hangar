<!--
~~~
title: "The Javascript Guide to Objects, Functions, Closures and Scope"
slug: /javascript-guide-to-objects-functions-closures-scope
date: 2011-12-06
publish: no
tags: [javascript]
~~~
-->

#The JavaScript guide to Objects, Functions, Closures and Scope

Throughout my time writing Javascript code, I've come to realize that while I love the language to bits, it is a little difficult to understand. A lot of people have attributed this to its  (admittedly not so great) design, or its obvious deviations from the paths well worn by other languages. Either way, understanding a few simple truths can go a long way with JS. 

Let's dive in with objects and variables. 

    var name = 'marvin';
    var age = 42;

That was easy. The `var` keyword restricts the scope of the variables and prevents them from going global without us knowing, but we'll talk about that a little later. 

Now how about

	 var person = {name: 'marvin', age: 42};

That's a little more interesting. What we're doing here is creating one variable -`person`. That variable contains the `name` and the `age` in a simple key value store… a map, if you will. Interestingly, it turns out that all Javascript objects can be thought of this way; they're essentially all key-value maps, or dictionaries. 

Accessing the internals of these objects is easy:

	 alert(person.name);
	 alert(person.age);

What's more interesting is that this is equivalent to 

	 alert(person['name']);
	 alert(person['age']);

This is the first 'aha' moment for most people, because it starts to give you a hazy idea of power that this approach gives you. More on that soon, though. Let's move on. 

    var sayHello = function(){
        alert('Hello.');    
    };

There's a load of things to learn from the three lines above. First, this looks suspiciously like the way we normally define objects. That's because it is. Functions in JS are just glorified objects. Truth be told, they aren't even very glorified - they're just objects which can be 'called'.

    var sayHello = function(name, age) {
        return "Hello, I'm " + name + " and I'm " + age + "years old.";
    }

Simple enough. They take arguments and they return values. 

    var sayHello = function(person) {
        return "Hello, I'm " + person.name + " and I'm " + person.age + "years old.";
    }

So far so good. Call the function with your person, and you'd get what you expect. 

    sayHello(person);

So way to *call* a function *object* is to use `()`, possibly with arguments inside. 

But if functions are just objects themselves, then what's to stop us from doing this?

    person.doSomething = sayHello;

So `person.doSomething(person)` should give the same result. The `person` seems too much in there, though… if we're calling the function off a person, why pass the person in again? Let's try something else:

    var sayHello = function(person) {
        return "Hello, I'm " + this.name + " and I'm " + this.age + "years old.";
    }

So here we come to the question of **scope**. Scope is the environment, or world, that the function executes in.  





























