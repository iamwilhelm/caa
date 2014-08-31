---
layout: post
title:  "Session 11 Garbage Collector for Quadcopter Flight Control"
date:   2014-08-28 1:00:00
categories: architecture
---

## Garbage collection

Writing in a language like C can be difficult due to various bugs that result from dangling
pointers and memory allocation. 

Garbage collectors are constructs included in higher level modern programming languages
that automatically sweep up and deallocate objects in memory no longer being used. Memory
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

## How does basic garbage collection work?

### Mark and Sweep

One way that garbage collectors work is that they 1) make a pass at all the objects
currently living, and mark the ones that have no references to it anymore. 
And then 2) it makes another pass--the sweep--where we free all the marked objects.

#### The Mark

How can we tell if an object no longer is being used? First, we set all the objects in
memory to be marked. We can do this because the objects are in a continuous block of 
memory, and we know the size of each object. We can traverse this object list linearly
and set all the objects to be marked for deletion.

How can we tell if an object is still in use? We can build an object dependency
graph each time we allocate a new object, by keeping track of which objects reference
which other object. Then during garbage collection, we can start with the root object, 
and then traverse this directed graph, using a breath-first or depth-first traversal
algorithm and unmark each one that we can reach.

The objects that are left marked, are then the ones we should delete, because they were
unreachable. They were unreachable, because there are no objects referencing them 
any longer.

#### The Sweep

To sweep the memory, we can linearly traverse the object list and free the objects that
are left marked for deletion. 

This can be problematic in two ways.

First, the sweep can take a long time if there are a lot
of objects that were left for deletion. Hence, in a real time system, we may not be
able to afford that time to do garbage collection.

Second, the sweep can fragment the memory, so we have small chunks of deallocated memory
that's not big enough for any subsequent chunks.

There are different ways to handle this, which is covered in the references, under 
generational garbage collecting.

## Our solution

We suggested having two CPUs on our system, one running the quadcopter flight control,
and the other running garbage collection. However, having two CPUs increased battery 
consumption, as well as problems that arise from concurrent garbage collection. Having
references change on the fly while executing garbage collection can be challenging.

We then determined that any garbage collection didn't need to run sweep to completion,
as long as we were able to bookmark where we left off. And as long as mark can complete
the entire traversal of the object graph within the allotted time, we would be able to
stop garbage collection when the allotted time was up.

When looking up quadcopters run with Go, they run at 200Hz, with 5ms allocated for Go's
garbage collection, which they can explicitly stop.

Although this is an active field of research, we didn't seem to quite hit the nail on 
the head when describing the solution.

## Misc

The rest of the time, we talked about generational garbage collection, and other 
details. Jason from Appstore team is pretty knowledgeable about garbage collection,
so you should ask him.

## References

- [How does garbage collection work?](http://chaoticjava.com/posts/how-does-garbage-collection-work/)
- [How garbage collector works Part 1](http://dotnetfacts.blogspot.com/2008/05/how-garbage-collector-works-part-1.html)
- [Garbage collection and the other kind of heap](http://brpreiss.com/books/opus5/html/page414.html)
- [Back to basics: mark and sweep garbage collection](http://blogs.msdn.com/b/abhinaba/archive/2009/01/30/back-to-basics-mark-and-sweep-garbage-collection.aspx)
- [Wikipedia on Garbage Collectors](https://en.wikipedia.org/wiki/Garbage_collection_(computer_science))
- [Real-time garbage collection is real](http://michaelrbernste.in/2013/06/03/real-time-garbage-collection-is-real.html)
- [Java garbage collection for real time system](http://www.drdobbs.com/jvm/java-garbage-collection-for-real-time-sy/184410684)
- [Real-time Java, Part 4: Real-time garbage collection](http://www.ibm.com/developerworks/library/j-rtj4/)

