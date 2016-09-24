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

The limitation here is that it requires the range to be put into an array, storing it in memory. So a simple range into the billions would  break the entire function. So how can this function be optimized to join ranges without manually storing each part of it in an array?

An example that would destroy this first method is:
`breaking_ranges = [(1..200_000_000_000), (3..300_000_000_000)]`

We can assume if a number contains the minimum, the two ranges overlap. So we can check a range against all the other ranges. If there's overlap, we can set a new start point and end point, then create a new range from there.

```ruby
def better_range_maker(ranges_array)
combined_ranges = []
  ranges_array.each do |range|
    start = range.begin
    finish = range.end

    ranges_array.each do |other_range|
      if other_range.include?(start)
        start = other_range.begin
      end
      if other_range.include?(finish)
        finish = other_range.end
      end
    end
    range = Range.new(start, finish)
    combined_ranges.push(range) unless combined_ranges.include?(range)
  end
  return combined_ranges
end
```

This uses much less memory. But it also will iterate through the array twice, giving us an `On^2` runtime. So it isn't perfect, I know. 
