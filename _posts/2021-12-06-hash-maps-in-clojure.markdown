---
layout: post
title:  "Hash Maps in Clojure"
date:   2021-12-06 7:52:05 -0400
categories: clojure, apprenticeship
---

Hash-Maps are key-value data structures used to store values and lookup 
values based on some key. The collection is organized based on the hash
codes of the keys for optimal lookup performance.

You've probably seen and even used the hash-map notation in
different parts of your code.

````clojure
=> (hash-map :steak 10 :beef 8 :roast 9)
{:beef 8, :roast 9, :steak 10}
````

Maps and hash-maps are one in the same. So what happens behind the scenes
when you instantiate a map?

### Clojure's Bucket List

Hash-maps are divided into some arbitrary number of "buckets", or sublists.
Within each bucket is another list that holds the map key-value pair. The 
bucket as well as the position within the bucket that the value is placed 
under is determined by the hash value of the key.

For the keys in the earlier map, we would have the following hashes:
````clojure
user=> (map hash '(:steak :beef :roast))
(-1536444162 -1184525500 -359004292)
````

### Selecting the Buckets

Suppose we have 3 buckets in the hash-map. Using these hashes, a bucket can be selected
using the `mod` of each hash against the number of buckets available.

````clojure
user=> (map #(mod % 3) (map hash '(:steak :beef :roast)))
(0 2 2)
````

This means the first bucket would contain `:steak`, the second bucket would be empty, 
and the last bucket would have both `:beef` and `:roast`.

### Selecting a Bucket Position

After a bucket is selected, a position must be selected. One way to choose a bucket would be
finding the `mod` of the hash against the number of elements in the bucket's list.

Suppose each bucket has a 5 element list. Then the position of each item in their 
respective buckets would be as follows:
````clojure
user=> (map #(mod % 5) (map hash '(:steak :beef :roast)))
(3 0 3)
````

So what we have now is
- `:steak` at Bucket 0, Position 3
- `:beef` at Bucket 2, Position 0
- `:roast` at Bucket 2, Position 3

When we need to look up an item, the same process used to add the item to the hash-map is also
used to search the hash-map. When a key is requested, it is hashed and the bucket as well as the
position in that bucket is found, then the value at that position is returned to the caller.

````clojure
user=> (def cows {:beef 8, :roast 9, :steak 10})
#'user/cows
user=> (get cows :beef)
8
````


### Collisions

In a hash-map, a collision occurs when two hashes map to the same bucket and position.

This can occur when two different values produce the same hash...
````clojure
user=> (map hash '(:beef :roast))
(-1184525500 -1184525500)
````

...or this might occur if the bucket and position that two hash values map to are the same.
````clojure
user=> (map (fn [h] {:hash h 
                     :bucket (mod h 3) 
                     :position (mod h 5)}) 
            (map hash '(:beef :moo)))

({:hash -1184525500
  :bucket 2
  :position 0} 
 {:hash 684602810
  :bucket 2
  :position 0})
````

So how do we handle cases where two hashes result in the same position?

#### Chaining

One way to handle this is to make each position in the bucket a linked list. As items are added
to a hash-map, they are added to a linked list at the position it's expected at. When Items need 
to be retrieved, every item in that position is compared the original key object for equality.

#### Open Addressing

Another technique to handle collisions is Open Addressing, which can be implemented several 
different ways. The main idea of Open Addressing is to search for an unoccupied position in the 
bucket until an unoccupied position is found. That position is then used to store the key-value 
pair being added.

This technique can result in infinite looping unless handled in some way, such as growing the lists
as they reach maximum capacity.