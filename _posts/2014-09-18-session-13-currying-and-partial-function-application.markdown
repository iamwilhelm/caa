---
layout: post
title:  "Session 13 Currying and Partial Function Application"
date:   2014-09-18 5:00:00
categories: concept
---

## For loops are too much

When using a for loop, it's flexible, but contains too many details you don't 
actually care about.

    for(i = 0; i < arr.length; i++) {
      arr[i] = arr[i] * arr[i]
    }

We actually only care that we want to square every element. We don't actually
care how to iterate through the array, when to stop iterating, and how to access
the array.

    _.map(arr, function(elem) {
      return elem * elm
    })

Now, we don't have to care about how to iterate through the data structure. 
We only have to care about how to transform each element. But Underscore.js
has the argument order backwards. Instead, it should be:

    squarer = _.map(function(elem) { return elem * elem })
    squarer(arr)

So then map can be specialized, stored as a function, and used later. Because 
first class functions can be passed around, we can even pass it into a function
to customizable functions.

Essentially, we're partially applying the function, as a special case, so we 
can use it later.

## Partial Function Application

When you have a function with multiple arguments, we usually call it like so:

    foo(a, b, c)

We typically need all the arguments before we can call it. But what if we can 
partially call a function?

    bar = foo(a, b) # This returns a function

Where the function can be called again with the last parameter later on.

    bar(c)

An implementation might be:

    function partial(fn /*, args...*/) {
      // A reference to the Array#slice method.
      var slice = Array.prototype.slice;
    
      // Convert arguments object to an array, removing the first argument.
      var args = slice.call(arguments, 1);
       
      return function() {
        // Invoke the originally-specified function, passing in all originally-
        // specified arguments, followed by any just-specified arguments.
        return fn.apply(this, args.concat(slice.call(arguments, 0)));
      };
    }

## Currying

Currying is related to PFA, but a separate concept. It's a way to turn functions 
with the signature foo(a, b, c) into foo(a)(b)(c). We're turning one function with
mutliple arguments into multiple functions that take one argument

    function curry(func: Function, arity: number) {
      return function(x) {
        if (arity === 1) {
          return func(x);
        } else {
          return curry(func.bind(null, x), arity - 1);
        }
      };
    }

    function add(...args: number[]) {
      return args.reduce(function(a,b) { return a + b; })
    }

## Composition

    comp2 :: (a -> b) -> (b -> b -> c) -> (a -> a -> c)
    comp2 f g = (\x y -> g (f x) (f y))

## References

- [What's the difference between Currying and Partial Application?](http://raganwald.com/2013/03/07/currying-and-partial-application.html)
- [Partial Function Application is not Currying](http://www.uncarved.com/blog/not_currying.mrk)
- [Parital Application in Javascript](http://benalman.com/news/2012/09/partial-application-in-javascript/)
- [Curried Javascript Function](http://www.crockford.com/javascript/www_svendtofte_com/code/curried_javascript/index.html)
- [Higher Order Functions](http://learnyouahaskell.com/higher-order-functions)
