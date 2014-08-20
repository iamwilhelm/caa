---
layout: post
title:  "Session 10 Generator-based flow control and coroutines"
date:   2014-08-20 05:03:51
categories: concept coroutine generator
---

## What's wrong with callbacks?

Callback hell is not just a problem with syntax. It makes the code brittle and hard
to refactor.

    doAsync1(function () {
      doAsync2(function () {
        doAsync3(function () {
          doAsync4(function () {
        })
      })
    })

## A Basic Iterator

An iterator is an object that allows the programmer to traverse a data structure,
without knowing anything about how to traverse it.

    arr.each { |a| puts a }

In this example in ruby, `each` is an iterator. It lets me traverse an array without
knowing how it's stored or how to traverse it.

## A Basic Generator

A generator is a function that generates iterators. The iterator you get back, you can
call `next()` to get the next piece of data. We can define one like so:

    function* helloWorldGenerator() {
        yield 'hello';
        yield 'world';
    }

    var hw = helloWorldGenerator();
    console.log(hw.next()); // prints { value: 'hello', done: false }
    console.log(hw.next()); // prints { value: 'world', done: false }
    console.log(hw.next()); // prints { value: undefined, done: true }

A generator gives us an iterator `hw` that we can continuously call next() upon. The
`yield` keyword lets us suspect execution of the function until whatever we yielded
to finishes.

It's also good for generating infinite series of values, streaming data, and 
lazy evaluation.

## How does this solve the callback problem?

As an example of a toy library that wraps up generators for us, we can write the
code as:

    var fs = require('fs');

    thread(function *(){
      var a = yield read(‘Readme.md’);
      var b = yield read(‘package.json’);
      console.log(a);
      console.log(b);
    });

    function read(path) {
      return function(done){
        fs.readFile(path, ‘utf8', done);
      }
    }

This can also catch thrown exceptions. Now that we're yielding results, we can stack
the calls to look like synchronous calls by using the `yield` keyword.

The toy library can be implemented:

    function thread(fn) {
      var gen = fn();

      function next(err, res) {
        var ret = gen.next(res);
        if (ret.done) return;
        ret.value(next);
      }
      
      next();
    }

All the library does is coordinate the generation of values from the programmer's
code block, and set up the next yielding function to call.

## Full fledge libraries

    var suspend = require('suspend'),
        fs = require('fs');

    suspend(function*(resume) {
        var data = yield fs.readFile(__filename, 'utf8', resume);
        if(data[0]) {
            throw data[0];
        }
        console.log(data[1]);
    })();

[Suspend](https://github.com/jmar777/suspend), [Co](https://github.com/visionmedia/co),
[Continuable](https://github.com/Raynos/continuable) are all usable.

## References

- [What's the Big Deal with Generators?](http://devsmash.com/blog/whats-the-big-deal-with-generators)
- [A Study on Solving Callbacks with Javascript Generators](http://jlongster.com/A-Study-on-Solving-Callbacks-with-JavaScript-Generators)
- [A Closer Look at Generators without Promises](http://jlongster.com/A-Closer-Look-at-Generators-Without-Promises)
- [Callbacks vs Coroutines](https://medium.com/code-adventures/callbacks-vs-coroutines-174f1fe66127)
- [Managing Node.js Callback Hell with Promises, generators, and other approaches](http://strongloop.com/strongblog/node-js-callback-hell-promises-generators/)
- [Wikipedia entry for Generator](https://en.wikipedia.org/wiki/Coroutine#Comparison_with_generators)
