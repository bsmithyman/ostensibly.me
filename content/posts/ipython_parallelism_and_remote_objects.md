---
title: IPython parallelism and remote objects
description: Some thoughts on parallelism with IPython and persistent worker objects
date: "2014-11-17"
categories: 
    - "python"
    - "parallel"
    - "geophysics"
---

IPython parallel workers are hard to write. Basically, these things are easy when you're running a parallel map (calling the same function over multiple inputs) with a function handle defined in the notebook. When you try to set up persistent objects on the workers and then refer to them later, things get hairy. Why? *Successfully implementing persistent workers in IPython seems to be about 90% fussing with namespaces.*

## Background

Example: [bsmithyman/zephyr:integration/Frontend.py](https://github.com/bsmithyman/zephyr/blob/76a36f3e7bb8467a7374a1fd25ecfe132e0fa92e/zephyr/Frontend.py)

I've been making some progress with the structure of parallel problems. There turn out to be a ton of namespace issues when you try to put persistent objects on workers. It's doable, though. The main trick to getting persistent workers to function on remote nodes comes down to how Python namespaces work. 

IPython has a decorator (IPython.parallel.interactive) that can make it as though a function was typed into the interactive console, from a namespacing point of view.

Adding the `@interactive` decorator changes the scope of its argument such that if you were to define functions like so (in module `zephyr.Frontend`):
````
def myFunction1():
    return __name__

@interactive
def myFunction2():
    return __name__
````

and then call them:

````
>>> print(myFunction1())
zephyr.Frontend

>>> print(myFunction2())
__main__
````
you'd see that they have different scopes.

## How is this useful?

The importance comes when you're trying to set up an object in that scope. For example, in the file above (*Frontend.py*), the goal is to initialize a set of parallel worker objects that are instances of the class `Kernel.SeisFDFDKernel`.

Normally, if a function were called to initialize them, they would lose scope as soon as a function returned. Hence, just calling `dview.map` to construct them doesn't work. You can't call a class constructor directly with a parallel map (at least, not without some major issues).

The next obvious choice is to try to set things up as globals:
````
def setupCache(systemConfig):

    global localSystem
    global baseSystemConfig

    localSystem = {}
    baseSystemConfig = systemConfig
````

It seems like this should work, except that if the `setupCache` function is defined in the scope of the module (`zephyr.Frontend`), then the calls to `global` inside that function will be relative to that scope. Basically, the functions can't break out of the module scope without disturbing degrees of poking around in private namespace constructs.

Adding the `@interactive` decorator means that the functions in *Frontend.py* can work in the namespace of the interpreter (i.e., within `__main__`). This would probably not be the best idea in all cases, but it's particularly useful on parallel workers. Calls to `dview.map` can work on class methods. Objects on the same worker can share datastores. There's a lot of good stuff that becomes possible.

Still lots of work to do, though.