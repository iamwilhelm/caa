---
layout: post
title:  "Session 10 Generator-based flow control and coroutines"
date:   2014-08-20 05:03:51
categories: concept
---

For imperative programming languages, it's implicit that statements execute one after
another. The next statement won't start running until the current one finishes.
However, there are instances where we want to run code in parallel. However, writing
asynchronous programs with thread is hard, because mutexes and multi threading is
hard to reason about.

Node.js leveraged the [reactor pattern][reactor] to make asynchronous programming
easier, using each callback as the atomic unit of execution.

## What's wrong with callbacks?

While using callbacks is easier than reasoning about mutexes, we get something people
call, "Callback hell" or "The pyramid of doom"

{% highlight javascript %}
    doAsync1(function () {
      doAsync2(function () {
        doAsync3(function () {
          doAsync4(function () {
        })
      })
    })
{% endhighlight %}

The problem with callbacks is not just a problem with syntax. It makes the code
brittle and hard to refactor. When you want to change the order of the code, it
takes a lot of code flinging, especially if there are 

## A Basic Iterator

To understand a generator, let's first figure out what an iterator is.

An iterator is an object that allows the programmer to traverse a data structure,
without knowing anything about how to traverse it.

{% highlight ruby %}
arr.each { |a| puts a }
{% endhighlight %}

In this example in ruby, *each* is an iterator. It lets me traverse an array without
knowing how it's stored or how to traverse it.

Internally in *each*, a *next()* function is implemented where every call to *next()*
gives you the next element of the array. In the ruby example above, that value is
passed to the block as the variable *a*.

You can think of the iterator as a machine that lets you grab elements from an array
every time you pull a lever called *next()*.

## A Basic Generator

A generator is a function that gives you a bunch of values, one at a time when you
ask for it. Like an iterator, it's like a machine with a lever that you pull when
you want the next value. And like an iterator, we call *next()* on a generator
to give us the next value.

We can define a generator like so:

{% highlight javascript %}
    function* helloWorldGenerator() {
        yield 'hello';
        yield 'world';
    }
    
    var hw = helloWorldGenerator();
    console.log(hw.next()); // prints { value: 'hello', done: false }
    console.log(hw.next()); // prints { value: 'world', done: false }
    console.log(hw.next()); // prints { value: undefined, done: true }
{% endhighlight %}

Here, the generator definition is defined with *function\**. And when we execute the
definition, we get *hw*, which is our generator. Our *hw* generator generates the
strings *hello* and *world*, in succession, when we pull the lever *next*

We can pull the lever until there are no more yields to be had, and then the 
generator returns an object with two keys. One key called *done* to indicate when the
generator has no more values to yield. And one key called *value* to indicate the 
next value that was yielded.

The *yield* keyword denotes whatever comes after yield is the value that gets returned
when *next()* is called. *yield* is the mechanism that lets us suspect execution of
the current function, to let something else execute in the mean time.

We can use this mechanism to do flow control if we only called *next()*,
when a yielded function has finished. Then, the next yielded function would run, until
all the yieldable functions in the generator have finished running.

## How does this solve the callback problem?

As an example of a toy library "thread" that uses generators to do flow control for us,
we can write our async code now as:

{% highlight javascript %}
    var fs = require('fs');

    thread(function *(){
      var a = yield read("Readme.md");
      var b = yield read("package.json");
      console.log(a);
      console.log(b);
    });

    function read(path) {
      return function(done){
        fs.readFile(path, "utf8", done);
      }
    }
{% endhighlight %}

Notice that the asynchronous calls we want to run one after another can be denoted
by yielding to that async call, instead of using nested callbacks. As a result, 
our code look like synchronous calls by using the *yield* keyword.

The toy library can be implemented as:

{% highlight javascript %}
    function thread(fn) {
      var gen = fn();

      function next(err, res) {
        var ret = gen.next(res);
        if (ret.done) return;
        ret.value(next);
      }
      
      next();
    }
{% endhighlight %}

All the library does is coordinate the generation of values from the programmer's
generator definition, and set up the next yielding function to call.

When *gen* is created, we go into the generator until we hit the first *yield*. When
we do, we don't execute *read("Readme.md")* immediately. We yield the execution in 
the generator until the generator's *next()* is called. This doesn't happen until
the execution calls *next(err, res)*, and inside of it *gen.next()* is called.

That is when execution resumes in the generator, and we get the value of the
generator back. The value of the generator is an object, which contains two keys:
*value* and *done*. The *value* of the generator return is a function, because
the yielded *read("README.md")* returns functions. The *done* of the generator return
is a boolean indicating whether the generator is finished yielding all its values.

So if there are no more values to yield, then we return out of *next(err, res)* because
we're done.

But if we're not, then we get the value of the return from the generator
(*ret.value*). *ret.value* is a function because the thing that's yielded,
*read("README.md")* returns a function. And hence, we can pass *next(err, res)* into
it so that *next(err, res)* can execute again when this yielded function finishes.

Essentially, all *thread()* does is manage the pulling of the lever (*next()*) only
when a yielded function has finished. That gives us the illusion of being able to 
write synchronously, when the underlying mechanism is asynchronous. In a way, we're
abusing the *yield* mechanism, normally used for thread execution control, but 
managed in a way that the yielded functions are guarenteed to execute only when 
the previous one has finished.

The advantage of using generator based flow control is that this can also catch
thrown exceptions. 

## What else are generators good for?

It's also good for generating infinite series of values, streaming data, and 
lazy evaluation.

## Full fledge libraries

{% highlight javascript %}
    var suspend = require('suspend'),
        fs = require('fs');

    suspend(function*(resume) {
        var data = yield fs.readFile(__filename, 'utf8', resume);
        if(data[0]) {
            throw data[0];
        }
        console.log(data[1]);
    })();
{% endhighlight %}

[Suspend][suspend], [Co][co], and [Continuable][continuable] are all libraries that use
similiar mechanics to leverage generators for flow control.

## References

- [What's the Big Deal with Generators?](http://devsmash.com/blog/whats-the-big-deal-with-generators)
- [A Study on Solving Callbacks with Javascript Generators](http://jlongster.com/A-Study-on-Solving-Callbacks-with-JavaScript-Generators)
- [A Closer Look at Generators without Promises](http://jlongster.com/A-Closer-Look-at-Generators-Without-Promises)
- [Callbacks vs Coroutines](https://medium.com/code-adventures/callbacks-vs-coroutines-174f1fe66127)
- [Managing Node.js Callback Hell with Promises, generators, and other approaches](http://strongloop.com/strongblog/node-js-callback-hell-promises-generators/)
- [Wikipedia entry for Generator](https://en.wikipedia.org/wiki/Coroutine#Comparison_with_generators)

[reactor]: http://jeewanthad.blogspot.com/2013/02/reactor-pattern-explained-part-1.html
[suspend]: https://github.com/jmar777/suspend
[co]: https://github.com/visionmedia/co
[continuable]: https://github.com/Raynos/continuable
