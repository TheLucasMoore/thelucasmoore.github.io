---
layout: post
title: Database Transactions with Postgres and ActiveRecord
author: Lucas Moore
---

In refactoring my [anagram API project](http://dev.thelucasmoore.com/2016/09/01/Building-An-Anagram-API.html), I was asked how to handle multiple requests that were attempting to `POST` words to the database and shared a key.
As the code currently is, one request would pull out and modify the table row. The second concurrent request would therefore be silently lost.
That's no good.

Here's the code I had.

```ruby
data['words'].each do |word|
  anagram = Anagram.find_or_create_by(key: find_key(word))
  if anagram.words.exclude?(word)
    anagram.words.push(word)
  end
  anagram.save
end
```

This be resolved with a [transaction](http://api.rubyonrails.org/classes/ActiveRecord/Transactions/ClassMethods.html).

A transaction is a block wrapped around SQL statements, which will only persist if there are no errors in the block.
This means nothing will be executed until the entire block is valid. The changes happen as one structure, not many pieces.

For the two concurrent requests, by wrapping the logic in a `.transaction` block, it will now tell us if it fails with a database rollback.

Transactions can be called on a class method or an instance method. In this refactor, I chose to call the `.transaction` on the `anagram` variable which is the instance of the Anagram with that particular key.

I could call it on the Anagram model itself, but by moving it down to one specific instance of the Anagram class, it catches the specific instance of trying to modify one row of the table at the same time.  

``` ruby
data['words'].each do |word|
  anagram = Anagram.find_or_create_by(key: find_key(word))
  anagram.transaction do
  if anagram.words.exclude?(word)
    anagram.words.push(word)
  end
  anagram.save!
  end
end
```

Also note that I changed `.save` to `.save!`, which raises an exception if it doesn't execute correctly, instead of simply returning `false`. This is needed to make sure the block will not run if saving doesn't successfully execute.

Interestingly, both the `.save` and `.destroy` methods are already wrapped in a transaction in ActiveRecord. So the rollback errors I've been seeing when attempting to save an invalid database entry have actually been thanks to transactions all along.
