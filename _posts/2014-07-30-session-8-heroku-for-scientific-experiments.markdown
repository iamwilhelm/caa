---
layout: post
title:  "Session 8 Heroku for Scientific Experiments"
date:   2014-07-30 17:00:00
categories: architecture
---

Back in the 90's when the internet was getting started, people ran their own servers. 
As the internet matured, 

Much credit can be given to Jeff Bezos of Amazon when he compared the availability of
computation to the availability of utilities, like water and electricity. Just as 
companies use to build their own electric generators at the turn of the century, people
use to build their own servers.

This was also made possible by virtualization software by VMware. 

Now, it's very easy to spin up servers as you need them with AWS. [Heroku][heroku]
makes it not only simple to spin up servers, but to also deploy code.

Currently, scientific experiments are mostly done by lab rats and graduate students
manually. To set up experiments, there's a lot of tedious manual work pipetting different
variations of the experiment.

How can we design a lab that makes it just as easy to run an experiment as it is to
bring up a virtual server? What sorts of experiments would we be able to run? How 
should be constrain the problem for the initial set of users?

[heroku]: https://heroku.com

