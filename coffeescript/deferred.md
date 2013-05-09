If you've been following the development of jQuery noticed the additions in 1.5, or if you've been writing a lot of asynchronous code, chances are you've heard, seen or used some form of a deferred library. 

What is a deferred style, though? While there are a ton of pedantic definitions out there, I'm just going to say that it's a method of putting off running bits of code until some specific thing has already happened. 

While this hits home very easily for anyone who has ever written any AJAX code, deferred code is a lot more than just running a callback after the server has returned a response. Here are some examples I can think of in my own projects:

* Render the page after authentication is done.
* Load a list of interests friends or members after determining if the user is a person or an organization.
* Add a set of elements into a container after the container has been rendered and added to the DOM.
* Run sequential asynchronous actions.
* Render the user information after all the individual bits of data have arrived.
* Easier callbacks for normal AJAX.
* Render something after multiple validity checks have been performed.

Because deferred programming addresses a need that is so pervasive and universal, there have been some attempts at creating a standard API to write and run code in a deferred style. The most visible one is the [CommonJS Promises](http://wiki.commonjs.org/wiki/Promises) draft, which jQuery follows. There are also implementations that don't meet a particular standard, but are very popular and useful. 

This is an exploration of sudhirj/simply-deferred was created. Simply Deferred was created to fill the void in Zepto - a modern Javascript library that has a jQuery like API but without the size, cruft and lag that is usually attributed to jQuery. Unfortunately, as part of the pruning process, the creators chose not to create a deferred library. Simply Deferred fills that gap. 

#### So how would a deferred library work?

Simply speaking the core of a deferred library is a deferred object: one that represents some process that is asynchronous. This objects lets you observe the be notified of changes in the state of the process that it represents. A lot of people also call this object a *promise*.

For instance, you could create a promise to represent an authentication check. If you wanted to do something after the authentication was done, you could write `authenticationPromise.done(function(){ doSomethingElse() })`. On the other side, the code that actually handles the authentication process would call `authenticationPromise.resolve()` at some point of time, at which point `doSomethineElse()` would automatically get called.

Besides `promise.done(...)`, most libraries will also provide a `promise.fail(...)` for failure cases. This method will similarly be called when `deferred.reject()` is called on the original object. We'd also like the methods to be chainable, so `promise.done(handleSuccess).fail(handleFailure).always(handleCleanup)` is easy to write.
