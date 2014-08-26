---
layout: post
title:  "Session 11 Garbage Collector for Quadcopter Flight Control"
date:   2014-08-28 1:00:00
categories: architecture
---

## Garbage collection

Writing in a language like C can be difficult due to various bugs that result from dangling
pointers and memory allocation. 

Garbage collectors are constructs included in higher level modern programming languages that
automatically sweep up and deallocate objects in memory no longer being used. Memory
management then no longer becomes the job of the programmer.

## Real time systems

Not all systems have the luxury of no time constraints of "I'll do it when I get around
to it." There are systems like flight control for quadcopters and airplanes that need to
make a decision on a periodic frequency and have hard deadlines to finish a computation.

Garbage collection can take up a significant portion of time, causing unacceptable delays
in real-time systems.

## Design

Given a quadcopter that has real-time constraints to make a decision on how much power,
and which direction to spin the propeller--how can we design a garbage collector with
real time constraints?

## References

- [How does garbage collection work?](http://chaoticjava.com/posts/how-does-garbage-collection-work/)
- [How garbage collector works Part 1](http://dotnetfacts.blogspot.com/2008/05/how-garbage-collector-works-part-1.html)
- [Garbage collection and the other kind of heap](http://brpreiss.com/books/opus5/html/page414.html)
- [Back to basics: mark and sweep garbage collection](http://blogs.msdn.com/b/abhinaba/archive/2009/01/30/back-to-basics-mark-and-sweep-garbage-collection.aspx)
- [Wikipedia on Garbage Collectors](https://en.wikipedia.org/wiki/Garbage_collection_(computer_science))
- [Real-time garbage collection is real](http://michaelrbernste.in/2013/06/03/real-time-garbage-collection-is-real.html)
- [Java garbage collection for real time system](http://www.drdobbs.com/jvm/java-garbage-collection-for-real-time-sy/184410684)
- [Real-time Java, Part 4: Real-time garbage collection](http://www.ibm.com/developerworks/library/j-rtj4/)

