<!--
~~~
title: Partial Functions / Currying in Python
publish: yes
tags: [python, functional, forklift]
slug: /partial-functions-in-python
~~~
-->

#Partial Functions / Currying in Python

Python, while being a fully object-oriented language, has a fairly rich set of functional programming tools too. I'm discovering more and more of them as a I go along, continually being surprised at how neatly functional programming solves some of the common problems I've seen. 

What I was trying to do was create a file change handler that ran on a separate thread and would be called when files were modified. The first attempt looked a bit like this:

    :::python
    def test(args):
        # run all the tests
    
    def watch(args):
        watch_and_run(['*.py'], test, args)

    def watch_and_run(patterns, function, argument):
        handler = FileChangeHandler(patterns, function, argument)
        watchdog.run(handler)
    
The reason `FileChangeHandler` was written that was because it was going to run the `test` method in a separate thread, like this:

    :::python
    class FileChangeHandler(PatternMatchingEventHandler):
        def __init__(self, function, arguments, *args, **kwargs):
            self._function = function
            self._argument = argument    
            super(FileChangeTestRunner, self).__init__(*args, **kwargs)
        def on_modified(self, event):
            self._function(self._argument)    

The problems with this approach are fairly obvious and show themselves quickly: 

* multiple arguments are currently impossible and will complicate the code a lot
* the code you're calling needs to know about the arguments 
* you can never be sure that your function will be called correctly by the code you're passing it to 

An elegant solution, in cases like this, is [currying](http://en.wikipedia.org/wiki/Currying). This basically involves creating a new function that calls the function you want to call with the arguments you want to pass in. 

The `functools.partials` method provides an easy way to do this. Most dynamic languages have their own helpers - in Javascript, [undercore.js](http://documentcloud.github.com/underscore/) has the `_.bind` method which lets you curry functions. It's a lot simpler to do in Javascript because of how easy it is to create anonymous functions, but Python manages just as well. 

Here's the new code using `functools.partial`:

    :::python
    class FileChangeHandler(PatternMatchingEventHandler):
        def __init__(self, handler, *args, **kwargs):
            self._handler = handler
            super(FileChangeTestRunner, self).__init__(*args, **kwargs)
        def on_modified(self, event):
            self._handler()

    def watch(args):
        watch_and_run(['*.py'], functools.partial(test, args))

What's happening here is that the `watch_and_run` is being passed the output of `functools.partial`, which is a function that, when run, will call `test` with `args` as the argument. Here's how it works (from the [documentation](http://docs.python.org/library/functools.html#functools.partial)):

    :::python
    def partial(func, *args, **keywords):
        def newfunc(*fargs, **fkeywords):
            newkeywords = keywords.copy()
            newkeywords.update(fkeywords)
            return func(*(args + fargs), **newkeywords)
        newfunc.func = func
        newfunc.args = args
        newfunc.keywords = keywords
        return newfunc

It defines a `newfunc`, which when called updates the original `args` and `kwargs` that you pass in with the ones that it is called with. It then calls the original function with these updated arguments (and stores the original values as properties that you can access).


[forklift]: http://www.runway7.net/forklift