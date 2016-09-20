---
layout: post
title: Ranges in Ruby
author: Lucas Moore
---

I was recently struggling through a coding exercise. I was tasked with creating a method that takes in an array of ranges and outputs a new, simplified set of ranges, which accounts for their overlap.

I did some [*refreshing on the Range docs*](https://ruby-doc.org/core-2.2.0/Range.html) (and had been searching for `.cover?` all along!)

I also found a simple way to [*sort and divide a Set by a block*](http://apidock.com/ruby/Set/divide), which will come in quite useful, as we'll see.

The input:
`[(2..6), (1..5), (8..9)]`

The desired output:
`[(1..6), (8..9)]`

Here's the code, with comments.

```ruby
require 'set' # we have to manually require the Set class
def join_ranges_together(ranges_array)
  set = Set.new
  joined_ranges_array = []
  # create a new Set and an Array where we'll store the new Ranges

  ranges_array.each do|range|
    range.each do|node|
      set.add(node)
      #this adds all the Range pieces into one Set
    end
  end

  # From docs on Set. Divides a block where the difference between two integers is 1.
  # This creates nested Sets where the numbers are in incremental order.
  sets = set.divide { |x,y| (x - y).abs == 1 }
  sets.each do |set|
  # iterate through the incremental sets, sorting and converting them to arrays
  set_array = set.to_a.sort
  # make a new Range based on sorted arrays, based on the array's start and end
  range = Range.new(set_array[0], set_array[-1])
  joined_ranges_array.push(range)
  end
  #finally, return the newly pushed together Ranges
  return joined_ranges_array
end
```
