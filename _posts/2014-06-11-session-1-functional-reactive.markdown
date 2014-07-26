---
layout: post
title:  "Session 1 Functional Reactive"
date:   2014-06-11 08:03:51
categories: concept functional
---

React.js is an example of a functional design to UI. There are two main ideas, 
There's only one way binding from data to UI components, and in order to speed up
rendering, we use a shadow DOM.

## One way binding

With other client-side frameworks, they use two way binding. When 
data gets updated, the UI gets updated. And when the UI gets updated, more data gets
updated. As a result, it's possible to get a ping-ponging effect between data updates
and UI updates that makes it hard to keep track of the data flow.

The concept behind functional reactive is that there's one-way binding between 
data and the results. That means that the data describes everything about the UI,
and the UI is always generated from scratch from the data. 

When there's an update in the data, the UI doesn't update itself, but floats the
event all the way back to the data to update the data. And then we generate the UI
from scratch from the updated data.

The design of functional reactive is that the data is the single source of truth 
in the system. When the data is the single source of truth, all views are generated
from it from scratch. 

The advantage of this design is that you can easily reason about the system, because
the data flow only goes one way. In addition, to implement undo is easy. You record
a history of data after every change, and simply go back in time.


## Immediate-mode vs Retained mode

You can also do culling in a system like this.

## Shadow DOM

You use the shadow DOM to speed up the problem of generating the UI from scratch 
every time. Instead of using the DOM itself, which can be slow, we generate a 
copy of the DOM, and diff the generated shadow DOM against the original shadow DOM.

## References

[Removing User Interface Complexity, or Why React is Awesome][http://jlongster.com/Removing-User-Interface-Complexity,-or-Why-React-is-Awesome]


