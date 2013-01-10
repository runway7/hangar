<!--
~~~
title: "Writing a Deferred Library from Scratch"
slug: /coffeescript/deferred
date: 2012-02-23
publish: no
tags: [javascript, coffeescript, deferred, from-scratch, promises]
~~~
-->

#Writing a Deferred Library from Scratch

If you've been following the development of jQuery noticed the additions in 1.5, or if you've been writing a lot of asynchronous code, chances are you've heard, seen or used some form of a deferred library. 

What is a deferred style, though? While there are a ton of pedantic definitions out there, I'm just going to say that it's a method of putting off running bits of code until some specific thing has already happened. 

While this hits home very easily for anyone who has ever written any AJAX code, deferred code is a lot more than just running a callback after the server has returned a response. Here are some examples I can think of in my own projects:

* Render the page after authentication is done.
* Load a list of interests friends or members after determining if the user is a person or an organization.
* Add a set of elements into a container after the container has been rendered and added to the DOM.
* Run sequential asynchronous actions.
* Render the user information after all the individual bits of data have arrived.
* Easier callbacks for normal AJAX.

Because deferred programming addresses a need that is so pervasive and universal, there have been some attempts at creating a standard API to write and run code in a deferred style. The most visible one is the [CommonJS Promises](http://wiki.commonjs.org/wiki/Promises) draft, which jQuery follows. There are also implementations in 