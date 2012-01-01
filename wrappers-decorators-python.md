<!--
~~~
title: "Decorators and Wrappers in Python"
slug: /decorators-wrappers-python
date: 2011-12-30
publish: yes
tags: [python]
~~~
-->

#Decorators and Wrappers in Python

Python has a really nifty language feature that I use and like a lot - decorators (or wrappers). They're great for writing a wide variety of reusable code that can work in a lot of different contexts. Using them is really easy, but writing them is a little unintuitive. Here's a quick and dirty guide. 

As an example, if you're using the (excellent) [webapp](http://code.google.com/p/webapp-improved/) microframework on GAE, you might be writing handler functions to handle your requests like this:

    :::python
    def show_page(request):
        # do stuff
        return response

Now if you need to ensure that this handler is accessible only to logged in users, you can always do something like this:

    :::python
    def show_page(request):
        authenticator.authenticate(request)
        # do stuff
        return response

but after doing this often enough, I realized that I actually want this protective code outside of my handler methods and that I don't want the code I write to know that it's being protected. Nor do I want to keep writing the protection code into my methods themselves. So I just use a **decorator**, like this:
    
    :::python
    @authenticate
    def show_page(request):
        # do stuff
        return response

A lot of libraries and frameworks offer useful decorators - one of my favorites is the `@property` decorator for Python itself, and the `@cached_property` that webapp offers. Here's a simply way to write the `@authenticate` decorator. 

    :::python
    def authenticate(func):
        def authenticate_and_call(*args, **kwargs):
            if not Account.is_authentic(request): 
                raise Exception('Authentication Failed.')
            return func(*args, **kwargs)
        return authenticate_and_call

This is why decorators are also called 'wrappers'. What Python does when it sees `@decorator` is to call the `decorator` function with the wrapped function as it's first argument (in this case the `show_page` function). 

The implicit contract is that the decorator will return another function that will be called in place of the decorated function. 
This new function that you return in the decorator might or might not call the function being decorated, but it's expected that it does. Wouldn't be very useful otherwise, would it?

So the `authenticate` decorator defines a new function called `authenticate_and_call` and returns this. This function is now going to be used instead of `show_page`. Calling it first checks if the request is authentic, and then calls `show_page`.

###Common Gotchas

Here's the important things to remember when writing a wrapper function: 

* Always accept and pass on all arguments - use `(*args, **kwargs)` when defining the wrapper and when calling the function. Exceptions are when you're intercepting arguments or modifying them. Look [here](/packing-and-unpacking-arguments-in-python) for more information about this.
* Don't forget to return the value that the wrapped function returns. I've lost track of the number of times I've found my decorated functions returning `None`, all because I forgot to type in `return`. The exception here is again if you want to intercept return values and check them for errors or do other things with them. 

A correctly written wrapper will also be usable without the `@decorator`, so
    
    :::python
    authenticated_show_page = authenticate(show_page)

should yield exactly the same result as using `@authenticate` on `show_page`. You might even prefer this way of doing things sometimes, especially if you want to expose both the wrapped and unwrapped methods. 

###Multiple decorators

It's also possible to use multiple wrappers on your methods:
    
    :::python
    @decorator2
    @decorator1
    def some_method():
        # do stuff

The order does matter, making this exactly the same as 

    :::python
    wrapped_method = decorator2(decorator1(some_method))

###Usage Ideas

If this is your first time with decorators, you might be wondering what to do with them. Here's some things I've seen and used them for (in all these cases, they let you add functionality transparently and without any changes inside the method):

* Caching: great for memoization and other caches
* Logging / Debugging: you can log method calls, arguments passed in and the values returned
* Authentication / Authorization: check if there is a current user and if the user can access this method
* Audit trails: keep track of the instances and the context in which a method is called
* Namespacing: establish the database namespace or context in which a method will run

These are just the common use cases - I'm sure there are tons more that I haven't seen yet.

###Meta decorators
Once you're comfortable with writing and using decorators, you might want to look at the last level of decorator-fu: meta decorators. These are functions that take an argument based on which they return a function that accepts the wrapped function and returns a function that wraps the wrapped function (I hope I actually got that right).

They're actually simpler than they sound, though. They're just decorators that take arguments which affect how they work. Here's an example:

    :::python
    def authorize(role):
        def wrapper(func):
            def authorize_and_call(*args, **kwargs):
                if not current_user.has(role): 
                    raise Exception('Unauthorized Access!')
                func(*args, **kwargs)
            return authorize_and_call
        return wrapper

    @authorize('admin')
    @authenticate
    def show_page(request):
        # do stuff

We've simply added another level to the way decorators normally work. In terms of code, this is the same as 

    :::python
    authorized_show_page = authorize('admin')(authenticate(show_page))

Another (simpler) form of writing this would be as a plain class:

    :::python
    class Authorize(object):
        def __init__(self, role):
            self.role = role        
        def __call__(self, func):
            def authorize_and_call(*args, **kwargs):
                if not current_user.has(role): 
                    raise Exception('Unauthorized Access!')
                return func(*args, **kwargs)
            return authorize_and_call

Both these methods are functionally equivalent - use whichever style makes more sense to you.             

So meta-decorators are a little more complex when written as functions, but easier when expressed as classes. Either way, they're pretty neat once you wrap your head around them. 

Have fun using decorators - they're a great way to clean up, cut down and make your code much more readable. The only (rare) problem is that they might make your functions a little more difficult to test because of the unrelated code that runs every time they're called. 

**Edits & Credits:**

* Thanks to [redditor hylje](http://www.reddit.com/r/Python/comments/nxjsp/decorators_and_wrappers_in_python/c3cqik6) for the idea of expressing decorators as simple classes. Makes things a lot easier to understand. 

* Thanks to [redditor Allanon001](http://www.reddit.com/r/Python/comments/nxjsp/decorators_and_wrappers_in_python/c3cx4pv) for pointing out that I'd forgotten the `return` in the decorator class example. 











