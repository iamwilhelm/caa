---
layout: post
title:  "Session 9 Edit distance and other distance measures"
date:   2014-08-13 08:03:51
categories: algorithm distance
---

## Problem

Given two words word1 and word2, find the minimum number of steps required to convert word1 to word2. (each operation is counted as 1 step.)

You have the following 3 operations permitted on a word:

- Insert a character
- Delete a character
- Replace a character
 
## Solution

    def levenshtein(orig, target)
      if orig.empty?
        return target.length
      elsif target.empty?
        return orig.length
      elsif orig[0] == target[0]
        return levenshtein(orig[1..-1], target[1..-1])
      else
        return [levenshtein(orig, orig[0] + target),
                levenshtein(orig, target[1..-1]),
                levenshtein(orig, orig[0] + target[1..-1])].min + 1
      end
    end

## References

[Fast and Easy Levenshtein distance using a Trie](http://stevehanov.ca/blog/index.php?id=114)

