---
layout: post
title:  "Session 12 Decision trees and information gain"
date:   2014-08-28 1:00:00
categories: algorithm
---

## Decision tree

Decision tree is a tool where each node is a decision point, and each path is a decision
you can make at that point. We can use the decision tree as a predictive model based on
observations about an event/object, to classify it.

## Information theory and Entropy

Information can be quantified as a measure of "surprise". The higher probability something
is the less surprised you are when it happens. The lower probability somethin gis, the
more surprised you are when it happens.

Let's say you have a stream of envelopes, and inside each envelope is a letter. There are
only 4 different possible letters in our alphabet, and each letter has a different
probability of appearing in an envelope. How surprised would you be, on average, as
you looked in each envelope in the stream?

This measure of average "surprise" is what we call Entropy. It is also the measure of
the average number of bits required to encode our alphabet of 4 letters.

Entropy is quantified as a measure of the average "surprise" of a randomize event.

  H[X] = -∑ p * log p

p is the probability of getting a yes answer. 

log p is the number of questions you need to ask at the most.

Hence, the average is sum of the probability times the number of times that even
would happen. Entropy of event X is denoted as `H[X]`.

## Information gain

Let's say we have two random events, X and Y that are not necessarily 
independent. How much more do I know about the entropy of X given Y?

Mutual information is

   I(Y ; X) = H(Y) - H(Y | X)


## References

- [Information Gain](http://www.autonlab.org/tutorials/infogain11.pdf)
- [Information Theory](http://www.fi.muni.cz/usr/staudek/infteo/info-lec.pdf)
- [Decision Trees](http://www.autonlab.org/tutorials/dtree18.pdf)

