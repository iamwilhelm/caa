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

![decision tree](http://www.cs.bham.ac.uk/~mmk/Teaching/AI/figures/dectree-orig.jpg)

We can use a decision tree as a classifier. Then the remaining question is, how do we
build a decision tree based on an example data set in order to classify future
data points?

How do we decide which attribute to split on first? The rest of this is the basis to
answering this question.

## Information theory and Entropy

### Question

You have a mailbox where you can send a constant stream of messages. Each message can 
contain one letter in our alphabet. There are only four letters in our alphabet,
A, B, C, and D. Each of the letters can appear with a probability of 1/4.

    P(X = "A") = 0.25
    P(X = "B") = 0.25
    P(X = "C") = 0.25
    P(X = "D") = 0.25

What binary encoding should you use to represent the entire alphabet efficiently?
How many bits do you need on average?

Now let's say that the probabilities aren't equal.

    P(X = "A") = 0.5
    P(X = "B") = 0.25
    P(X = "C") = 0.175
    P(X = "D") = 0.175

Now, what is a binary encoding that can represent the entire alphabet efficiently?
How many bits do you need on average?

### Information and Entropy

Entropy is the general case solution to the questions above. Entropy is
the smallest number of bits, on average, per symbol, needed to transmit a
stream of symbols drawn from a distribution of event X.

    Given:

    P(X = V1) = p1
    P(X = V2) = p2
    ...
    P(X = Vn) = pn

    Entropy is defined as:

    H(X) = -p1 log p1 - p2 log p2 - ... - pn log pn
    H(X) = -∑ (pn log pn)

or in Ruby

    p = [0.25, 0.25, 0.25, 0.25]
    p.inject(0) { |t, a| t += -a * Math.log(a) / Math.log(2) }

What does Entropy measure? It can be seen as a way to measure the shape of the
distribution. *High entropy means X is a uniform distribution. Low entropy means X
is from a varied distribution (with peaks and valleys)*

It can also be seen as the average number of bits you need to represent the distribution
of symbols in a random variable. The more random the variable (the more uniform the
distribution), the more bit you need to represent the alphabet.

Another way to think about entropy is the amount of uncertainty about a random variable.
The more uniform the distribution, the more uncertain you are of which letter will
appear. The more varied the distribution, the more certain you are of which letter 
will appear.

### Conditional Entropy

Conditional Entropy is a measure of entropy of a random variable predicated on knowledge
of the value of another random variable. We write it as such, and it's similar to 
conditional probabilities.

    H(Y | X)

Suppose we have a data set of what kind of chips people prefer (Random Var X), and
whether they are a fan of Keanu Reeves (Random Var Y).

    X               Y
    BBQ             yes
    Ranch           no
    Sour Cream      yes
    BBQ             no
    BBQ             no
    Sour Cream      yes
    Ranch           no
    BBQ             yes

The specific conditional entropy is:

    H(Y | X = BBQ) = -(2 / 4) * log (2 / 4) - (2 / 4) * log (2 / 4) = 1
    H(Y | X = Ranch) = -(0 / 2) * log (0 / 2) - (2 / 2) * log (2 / 2) = 0
    H(Y | X = Sour Cream) = -(2 / 2) * log (2 / 2) - (0 / 2) * log (0 / 2) = 0

Hence, the conditional entropy is the average across all the possible values of the
random variable X.

    H(Y | X) = P(X = BBQ) * H(Y | X = BBQ) +
               P(X = Ranch) * H(Y | X = Ranch) +
               P(X = Sour Cream) * H(Y | X = Sour Cream)
             = 4 / 8 * 1 +
               2 / 8 * 0 +
               2 / 8 * 0
             = 0.5

Conditional entropy tells us, what the distribution of Y, on average, if we know X. Seen
another way, it tells us how many bits we'd need to send, on average, to transmit 
an alphabet in Y, if we already know X. Or put another way, how uncertain are we about
Y if we already know X, on average?

### Information Gain

Now we've finally have the tools we need for information gain!

Information gain is the answer we get when we ask the question, "I must transmit 
whether people like Keanu Reeves. How many bits on average would I save if if both
ends already know what kind of chips they like?"

    I(Y | X) = H(Y) - H(Y | X)

Or put in another way, it tells us how well we would be able to predict Y, on average,
if we know X.

Put yet another way, how uncertain are we about Y if we already know X?

Information gain is also called Mutual Information.

In the previous example:

    H(Y) = 1
    H(Y | X) = 0.5
    IG(Y | X) = 1 - 0.5 = 0.5

That means, when we looked at the entropy of Y alone, we needed 1 bit to represent
whether they liked Keanu Reeves. However, if we already know X, on average, we only
need 0.5 bits to represent whether they like Keanu.

### How do we use Information Gain to build a decision tree?

Information gain can be used to determine which attribute to split on a decision tree
first. We want to choose the attribute that best discriminates between our
classifications first. The attribute that best discriminates between our
classifications is one that has the highest information gain.

In this case, whether they like Keanu or not is our classification (the random var Y).
The other random variable X is the attribute. If there are other columns/attributes,
say A, B, and C, those would be the data set columns.

Then we would calculate the information gain of I(Y | X), I(Y | A), I(Y | B), I(Y | C),
and pick the attribute with the highest information gain to split on.

Then we repeat the process for each branch, until we've exhaused our attributes.

## References

- [Information Gain](http://www.autonlab.org/tutorials/infogain11.pdf)
- [Information Theory](http://www.fi.muni.cz/usr/staudek/infteo/info-lec.pdf)
- [Decision Trees](http://www.autonlab.org/tutorials/dtree18.pdf)
- [What is Entropy and Information Gain?](https://stackoverflow.com/questions/1859554/what-is-entropy-and-information-gain)

