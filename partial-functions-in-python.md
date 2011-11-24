<!--
~~~
title: Partial Functions in Python
publish: no
tags: python, functional, forklift
slug: /partial-functions-in-python
~~~
-->

#Partial Functions in Python

Python, while being a fully object-oriented language, has a fairly rich set of functional programming tools too. I'm discovering more and more of them as a I go along, and I'm quite pleasantly surprised by now neatly functional programming solves some of the common problems I've seen. 

What I was trying to do was create a file change handler that ran on a separate thread and would be called when files were modified. The first attempt looked a bit like this:

    def test(args):
        # run all the tests
    
    def watch(args):
        watch_and_run(['*.py'], test, args)

    def watch_and_run(patterns, function, arguments):
        handler = FileChangeHandler(patterns, function, arguments)
        watchdog.run(handler)
    
The reason this had to to be done was because the `FileChangeHandler` was going to run the `test` method in a separate thread, like this:

    class FileChangeHandler(PatternMatchingEventHandler):
        def __init__(self, function, arguments, *args, **kwargs):
            self._function = function
            self._arguments = arguments
            super(FileChangeTestRunner, self).__init__(*args, **kwargs)
        def on_modified(self, event):
            self._function(self._arguments)    

Now that problems with this approach are fairly obvious and show themselves quickly. Some other functions that had to be run needed more than one argument, or a keyword argument. 

This also opens itself to an interesting design problem - how do you make sure that the code you're passing the function and arguments to actually makes the call correctly? 

[forklift]: http://www.runway7.net/forklift