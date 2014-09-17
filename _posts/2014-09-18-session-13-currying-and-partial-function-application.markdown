---
layout: post
title:  "Session 13 Currying and Partial Function Application"
date:   2014-09-18 5:00:00
categories: concept
---

## Partial Function Application

When you have a function with multiple arguments, we usually call it like so:

    foo(a, b, c)

We typically need all the arguments before we can call it. But what if we can 
partially call a function?

    foo(a, b) # This returns a function

Where the function can be called again with the last parameter later on.

    foo(a, b)(c)


## Currying

Currying is related to PFA, but a separate concept. It's a way to turn functions 
with the signature foo(a, b, c) into foo(a)(b)(c). We're turning one function with
mutliple arguments into multiple functions that take one argument

## Applications

This is more useful for languages where functions are first-class objects. When you 
can pass functions around, you can delay execution. 

## References

- [What's the difference between Currying and Partial Application?](http://raganwald.com/2013/03/07/currying-and-partial-application.html)
- [Partial Function Application is not Currying](http://www.uncarved.com/blog/not_currying.mrk)
- [Parital Application in Javascript](http://benalman.com/news/2012/09/partial-application-in-javascript/)
- [Curried Javascript Function](http://www.crockford.com/javascript/www_svendtofte_com/code/curried_javascript/index.html)
- [Higher Order Functions](http://learnyouahaskell.com/higher-order-functions)
