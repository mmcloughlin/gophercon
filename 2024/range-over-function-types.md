# Range Over Function Types

Since generics, users can define their own container types.

Example: `Set[T]` and a union function. How do you iterate over the elements?

* `Push`: provide a callback. Iteration happens 
* `Pull`: returns a `next` and `stop` function

Both of these approaches appear in the ecosystem.

Goal: improve ecosystem by providing a standard approach.

Gang of four: Iterator pattern.
Widespread in modern programming languages.

Go already has iterators for builtin container types.

Go 1.23 will support: `for/range` over function types

Supported function types:

```
func(yield func(V) bool)
func(yield func(K, V) bool)
func(yield func() bool)
```

New stdlib package [`iter`](https://pkg.go.dev/iter@go1.23rc1) contains types
`Seq[V]` and `Seq2[K, V]` for the above (no `Seq0`).

Iterator functions call the `yield` function on every element in the iterator.
If the `yield` returns false, iteration can stop.

> "Your first reaction might be: there's a lot of functions flying around here."

But:

* Once you get past the wrappers, the actual implementation is simple
* Using it is trivial

As a matter of convention, recommend containers call their iterator method
`All()`.

Function Types as Iterators: "Pull iterator" is a function such that each time
you call it, it gives you the next value. Push iterators take the next element
in iteration as an argument.

The stdlib `iter` package provides a function to turn a push iterator into a
pull iterator.

* Push: work with for/range. Have `iter.Seq` types in stdlib.
* Pull: next/stop methods.

The `slices` and `maps` package have associated iterators:

* https://pkg.go.dev/slices@go1.23rc1
* https://pkg.go.dev/maps@go1.23rc1

Note: you don't have to use `for` loops with iterators. They are just
functions.

In order to use iterators, you need `go 1.23` in your `go.mod`.

