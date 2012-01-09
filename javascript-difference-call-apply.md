<!--
~~~
title: "The Difference between Call and Apply in Javascript"
slug: /javascript-difference-between-call-apply
date: 2011-12-07
publish: no
tags: [javascript]
~~~
-->

#The Difference Between Call and Apply

One very common thing that trips me up when writing Javascript is knowing when to use `call` and when to use `apply`. If you're wondering what these methods are, or don't know how scope works in JavaScript, then it might make sense to read the [Javascript Guide][guide] first.

Let's look at some ways we might want to use them:

    :::javascript
    var person1 = {name: 'Marvin', age: 42, size: '2xM'};
    var person2 = {name: 'Zaphod', age: 42000000000, size: '1xS'};
    
    var sayHello = function(){
        alert('Hello, ' + this.name);
    }

    var sayGoodbye = function(){
        alert('Goodbye, ' + this.name);
    }

Now if you've read the [guide][guide], this example will look really familiar. You'd already know that writing the following code:

    :::javascript
    sayHello();
    sayGoodbye();

will give errors (if you're lucky), or just unexpected results (if you aren't). This is because both functions rely on their scope for the `this.name` data, and calling them without explicit scope will just run them in the scope of the current window. 

So how do we scope them? Try this:

    :::javascript
    sayHello.call(person1);
    sayGoodbye.call(person2);
    
    sayHello.apply(person1);
    sayGoodbye.apply(person2);

All four of these lines do exactly the same thing. The run `sayHello` or `sayGoodbye` in the scope of either `person1` or `person2`. 

Both `call` and `apply` perform very similar functions: they execute a function in the context, or scope, of the first argument that you pass to them. Also, they're both functions that can only be called on other functions. You're not going to able to run `person1.call()`, nor does it make any sense to do so. 

The difference is when you want to *seed* this call with a set of arguments. Say you want to make the `say…()` methods a little more dynamic:

    :::javascript
    var say = function(greeting){
        alert(greeting + ', ' + this.name);
    }
    
    say.call(person1, 'Hello');
    say.call(person2, 'Goodbye');











[guide]: http://hangar.runway7.net/javascript-guide-to-objects-functions-scope-prototpyes-closures