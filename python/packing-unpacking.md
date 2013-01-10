
Ever wondered what the `*args` and `**kwargs` that you keep seeing in Python functions mean? I certainly did. So I did some searching and it turns they're pretty nifty, not to mention useful. 

That `*` and the `**` operators both perform two different, but complementary operations depending on where they're used. When used in a method definition, like so:

```python
def __init__(self, *args, **kwargs):
    pass
```

They perform an operation called 'packing'. True to it's name, what this does is *pack* all the arguments that this method call receives into one single variable, a list called `args`. You can use any variable name you want, of course, but `args` seems to be the most common and Pythonic way of doing things. 

Once you have this 'packed' variable, you can do things with it that you would with a normal list. `args[0]` and `args[1]` would give you the first and second argument, respectively. You can also modify,  delete and re-arrange items in the list. 

So how do you pass these packed arguments to another method? Here's where *unpacking* comes in to play:

```python
def __init__(self, *args, **kwargs):
    # do some stuff
    super(AwesomeClass, self).__init__(self, *args, **kwargs)
    #                                            ^
    #                                        LOOK HERE!
```

So there's the same `*` operator again, but this time it's in the context of a method call. What it does now is explode the `args` array and call the method as if you'd typed in each variable separately. 

Here's another example that might make things a little more clear:

```python
def func1(x, y, z):
    print x
    print y 
    print z  				

def func2(*args):
    args[0] = 'Hello'
    args[1] = 'awesome'
    func1(*args)

func2('Goodbye', 'cruel', 'world!')
# Will print
# > Hello
# > awesome
# > world!
```

This happens simply because we're changing the first two arguments before passing them off to `func1`. 

The normal rules governing method definition apply here… calling `func2('a', 'b', 'c', 'd')` will raise an error because it will in turn call `func1` with four arguments, which it doesn't expect.

The same principle applies to `**kwargs` too, except that in this case it applies to keyword arguments, and `kwargs` turns out to be a `dict`. 

Combined together packing and unpacking lets you do a lot of things like:

* validate arguments before passing them on 
* set defaults for positional arguments
* create adaptors for different pieces of code / libraries
* modify arguments depending on context
* log calls to methods
* write better and more resilient wrappers 

I'm sure there's a lot more uses you can think of and see for these little `*`s. Have fun. 

**Edits & Credits:**

* Thanks to [Florian Mayer](http://twitter.com/#!/segfaulthunter) for pointing out that `super(self.__class__, self).__init__()` would result in infinite recursion if a subclass is written and used. `self` would still refer to the instance of the subclass when the call comes up the chain.