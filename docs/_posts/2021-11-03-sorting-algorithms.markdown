---
layout: post
title:  "Sorting Algorithms"
date:   2021-11-03 7:52:05 -0400
categories: clojure, apprenticeship
---

Today I watched a documentary about algorithms,
[The Secret Rules of Modern Living Algorithms][algorithms]. 
I highly recommend checking this out if you ever get the chance. 

The documentary talked about a number of algorithms–I'll be talking about
[Bubble Sort][bubble-sort], [Merge Sort][merge-sort], 
and [Quick Sort][quick-sort].

### Bubble Sort

Bubble Sort is probably one of the most simple and straightforward sorting 
algorithms. This algorithm gets its name because of the way larger elements 
"bubble" to the top of the list. With each pass through the array, the 
largest element will always appear at the top of the list.

The process for Bubble Sort:
1. Start with the first element in the collection
2. If this element is greater than the next element, swap the two elements
3. Move to the next element and compare/swap with the element after it
4. Repeat this process until you reach the end
5. If any swaps were made, perform Bubble Sort again
6. If no swaps were made, then the collection is sorted

While the implementation is simple, it performs significantly slower than 
most other sorting algorithms.
- Best Case: O(n)
- Average Case: O(n<sup>2</sup>)
- Worst Case: O(n<sup>2</sup>)

Because Bubble Sort always moves the greatest element to the top of the list,
one optimization might be only sorting up to the N-1 element on each pass.

### Merge Sort

Merge Sort works by continuously halving a list, sorting each side, and
merging the two lists back together in a sorted order. 

The process for Merge Sort:
1. If the array contains 0 or 1 element, do nothing. Continue otherwise.
2. Split the array in two
3. Merge Sort each side of the array
4. Merge each side of the list back together
   1. Start with the first items on each array
   2. Add the lesser two items to the new array
   3. Move to the next item in the list
   4. Continue adding items to the array until both lists are empty

One thing I found interesting about Merge Sort is that sorting doesn't 
actually happen until the last step when merging the two lists back together.
I hadn't realized this until I wrote out the steps.

The overall performance of Merge Sort is O(n log n), which is much faster 
than Bubble Sort, but would still be slower than Bubble Sort's "Best Case,"
O(n). Of course, we would only see O(n) on a list that's already sorted 
(so probably not very often).

### Quick Sort

This one was a little harder for me to wrap my head around. The main
idea to it is centered around "pivot" values and swapping elements based 
on how they relate to that value.

The process for Quick Sort:
1. Select a pivot value from the array
2. Swap the pivot value with the item at the end of the array
3. Starting from the beginning of the array, find the first element that 
is greater than the pivot
4. Starting from the end of the array, find the first element that is less 
than the pivot
5. Swap the two elements
6. Repeat steps 3-5 until the left index is greater than the right index
7. Now swap the pivot value with the item at the left index
   1. This should place the pivot value in its correct position
8. Quick Sort the elements to the left of the pivot
9. Quick Sort the elements to the right of the pivot

When choosing a pivot element, it's best to have an element that would
result in the most median value of the collection. This would give you
two equally sized partitions on the left and right of the pivot. Of course,
you can only make your best guess at what this value might be on an unsorted 
list.

It's quite elegant how this algorithm works. It's not directly apparent that 
a list would become sorted following these steps, but it works well.

The average performance for Quick Sort is O(n log n), with the worst case 
being O(n<sup>2</sup>). So it sits nicely between Bubble Sort and Merge Sort.

[algorithms]: https://www.youtube.com/watch?v=kiFfp-HAu64&t=2780s
[bubble-sort]: https://en.wikipedia.org/wiki/Bubble_sort
[merge-sort]: https://en.wikipedia.org/wiki/Merge_sort
[quick-sort]: https://en.wikipedia.org/wiki/Quicksort