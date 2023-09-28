# Blazing Fast Merge with Loser Trees

The story begins with a flamegraph!

Spending a huge amount of time in `heap.fix` (in Go stdlib).

What is that thing?

Heap used a priority queue.

What is the code doing at this point?

**k-way merge**

Simple linear scan: `k*n`.

"heap is commonly used, although a tournament tree is faster in practice"

Copied the "loser tree" algorithm from Wikipedia, didn't work!

Go to the _art of computer programming_!

It's like a tournament. Playing "games" on the way up to find the lowest,
store the losers along the way.

## How fast is it?

* k sequences
* N total number of values
* Initialize: k-1 comparisons (each player loses 1 game)
* Each time we remove a value we do 1 comparison at each level of the tree
  `log(k)`

Total `(k-1) + n*log(k)`

"Same asymptotic complexity as a heap"

## Implementation

Generic. Parameterized on value type, and sequence type.

Nodes ordered so that parent is half the number. Trick also used in heap.
(requires 1-up indexes)

Nodes:

* Values
* Leaf nodes also have pointer to the sequence they're drawing from

...

## Evaluation

70-90% faster on Postings Merge Benchmark

But, at the cost of much more memory.
"Percentage wise it's bad, but in the scheme of things not that bad."

## Issues

Wanted to apply this to another case (log merging).

In this case, could not use `constraints.Ordered`, since wanted to use it on
entries that are not integers (timestamps).

Had to switch to using a function comparitor, which took a performance hit vs
the generic version. (Still faster overall.)

