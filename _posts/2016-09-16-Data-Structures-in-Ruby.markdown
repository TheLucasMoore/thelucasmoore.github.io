---
layout: post
title: Data Structures in Ruby
author: Lucas Moore
---

As a developer without a CS degree, I've set out to learn more complex data types on my own. I'll share what I've learned and hopefully you'll learn something too.

Inspired by [Itsy Bitsy Data Structures](https://github.com/codemix/itsy-bitsy-data-structures/blob/master/itsy-bitsy-data-structures.js), I've got some examples of useful data structure below, all in Ruby. The examples here are simplified versions of other articles. Be sure to check out all the stuff I link to for more in depth examples.

## Linked List

A linked list stores data associations in an ordered set, linking one piece of data to others.
Ruby doesn't have a `List` object built in, but an `Array` is able to accomplish [most of the same objectives](https://www.sitepoint.com/rubys-missing-data-structure/).

Building a [simple linked list in Ruby](http://matt.weppler.me/2013/08/14/implementing-a-linked-list-in-ruby.html), which allows us to see the link between two data points, as well as the next data point looks like this:

```ruby
class Node
  attr_accessor :node, :next

  def initialize(node)
    @node = node
  end

  def self.node_list(node, msg = nil)
    msg ||= ""
    return msg[0..-4] if node.nil?
    node_list(node.next, msg << "#{node.node} -> ")
  end
end
```

## Stacks and Queues

Again, Ruby can do these things with `Array`.

A stack is simply an array that has a bias towards the top (first) and bottom (last) of the stack. Here `.push` and `.pop` on an Array will work in much the same way as a Stack.

A Queue behaves like, well a queue. It's a stack that removes data from the front of the line. By limiting the functionality of an Array to `.push` and `.shift`, we can build a queue in Ruby.

## Hash Table

For hash tables Ruby luckily has the [Hash class](https://ruby-doc.org/core-1.9.3/Hash.html) to play around with. A hash stores key/value pairs for quick access to the precise data we want. There's some snazzy uses built right into Ruby, like `.invert` and `select` which take a block.

Here's an example using `Hash.new`.

```ruby
h = Hash.new
# (AND/OR)
h = { "a" => 100, "b" => 200 }
h.fetch("a")
#=> 100
h.delete("b")
h.fetch("b")
#=> KeyError: key not found: "b"
```

## Binary Search Trees

This data structure blew my mind when I first encountered it.
This visualization from [Itsy Bitsy Data Structures](https://github.com/codemix/itsy-bitsy-data-structures/blob/master/itsy-bitsy-data-structures.js) helps a lot.

```javascript
* This makes the traversal find a value very efficient. Say were trying to
* find the number 5 in our tree:
*
*             (4)         <--- 5 > 4, so move right.
*           /     \
*        2         (6)    <--- 5 < 6, so move left.
*      /   \       /   \
*     1     3    (5)    7 <--- Weve reached 5!
*
* Notice how we only had to do 3 checks to reach the number 5. If we were to
* expand this tree to 1000 items. We go:
*
*   500 -> 250 -> 125 -> 62 -> 31 -> 15 -> 7 -> 3 -> 4 -> 5
*
* Only 10 checks for 1000 items!
```

To build this in Ruby is quite simple. More details in this [source](http://zvkemp.github.io/blog/2014/04/25/binary-search-trees-in-ruby/). A very simplified version looks like this:

```ruby
module BinaryTree
  class Node
    # our three features:
    attr_reader :value
    attr_accessor :left, :right

    def initialize(v)
      @value = v
    end

    def insert(v)
      case value <=> v
      when 1 then left.insert(v)
      when -1 then right.insert(v)
      when 0 then false # the value is already present
      end
    end
  end
end
```
