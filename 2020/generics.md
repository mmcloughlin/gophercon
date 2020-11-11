# Typing [Generic] Go

Robert Greisemer (and Robert Lance Taylor)

Getting up-to-date with latest thinking on Generics.

Influenced by Featherweight Go.

## Draft Design

Abandoned contracts in favor of interfaces.

Simplified the design and 100% backwards compatible.

New Features:

1. Type parameters
2. Constraints
3. Type inference (optional)

If you understand type parameters and constraints, you understand Generics in
Go.

## Parameters

Type parameter list is like a regular parameter list but with square brackets
`[]`.

```
[P, Q, aConstraint, R, anotherConstraint]
```

Capitalize constraints

## Motivation

Sorting.

The interface is a pain in the ass.

What we really want is just a generic `Sort()` function.

Type parameters to the rescue:

```
func Sort[Elem ?](list []Elem)
```

Note: We need to be able to compare `Elem`.

This is what the constraints are for. Constraints are `interface`s. Type
satisfies a constraint if it implements the interface. So:

```
func Sort[Elem interface{ Less(y Elem) bool}](list []Elem)
```

Now if we have a `book` type with a `Less` method we can sort it with
`Sort[book](bookshelf)`.

## Type Checking

`Sort[book]` is called instatiation. Happens at compile time.

1. Subtitute type for type parameter `Elem`
2. Verify type constraints
3. Instatiate ordinary function `#Sort[book]`
4. Proceed as a regular function

That is:

1. Type check _instantiation_
2. Type check _invocation_

You can even separate them explicitly:

```
booksort := Sort[book]
booksort(bookshelf)
```

## Types can be Generic

For example:

```
type Lesser[T any] interface{
    Less(y T) bool
}
```

Note builtin `any` can currently only be used as a constraint, but it might be
natural to extend its use elsewhere in future.

> A generic function or type must be instantiated before it is used.

## Example: Sort Internals

```
func Sort[Elem interface{ Less(y ELem) bool }](list []Elem) {
    // ...
    if list[i].Less(list[j]) {
        // ...
    }
    // ...
}
```

Bear in mind that: `Elem` is a real _generic_ type, it is not an interface
type.

## Type Inference

In the bookshelf case above, it can infer `Elem == book` by a process called
_type unification_. This compares corresponding parts of types and deduces the
generic types.

Type unification only works for types in parameter list.

So it's actually:

1. If no type arguments are provided: use arguments to deduce by type
   unification.
2. Type check the instantiation
3. Type check the invocation

## What's Missing

Constraints can only describe method requirements.

What about `Sort([]int{1,2,3})`?

Address this by allowing a constraint interface to specify a list of types:

```
type Float interface {
    float32, float64
}
```

So we can now define a `min` function like:

```
func min[T Ordered](x, y T) T {
    if x < y {
        return x
    }
    return y
}

type Ordered interface {
    type int, int8, ...
}
```

The `x < y` is allowed because it's allowed on all types in the list.

## Type Parameters

We can express that we need a pointer to another type.

```
type Pointer[T any] interface {
    type *T
}

func f[T any, PT Pointer[T]](x T)
```

Or inline:

```
func foo[T any, PT interface{type *T}](x T)
```

## Arrived (mostly)

We need two functions, for basic types and methods.

```
func BasicSort[Elem Ordered](list []Elem)
func Sort[Elem Lesser[Elem]](list []Elem)
type Lesser[Elem any] interface {
    Less(Elem) Elem
}
```

## How Happy Are We?

* Type parameters - Yes
* Interfaces as constraints - Yes
* Type Lists - hmm, maybe, not in other languages
* Syntax (`[]`) - controversial, but good
* Type inference - Yes
* Fit with rest of Go - Yes!

In summary, we have a "fairly nice" design.

Fine tuning: type lists.

## Thoughts

* Type parameters will be a new tool in the toolset
* Orthogonal to the res tof the language
* New dimension of coding styles

In the beginning of Go, we used channels too much.

The same will happen with Generics!

But, it provides an easy avenue for unecessary generics.

## Example: ReadAll

We could make `ReadAll(r io.Reader)` generic:

```
func [reader io.Reader]ReadAll(r reader)
```

Generic code doesn't really solve any problems, it won't be much faster.

## Example: Channel Libraries

```
// Drain drains any elements remaining on the channel.
func Drain[T any](c <-chan T)

// Merge merges two channels of some element type into
// a single channel.
func Merge[T any](c1, c2 <-chan T) <-chan T
```

Yes! Enables code that is not possible otherwise.

## Guidelines

1. Improved Type Safety
2. More efficient memory use.
3. (Significantly) better performance.

Generics are glorified (type-checked) macros. Think twice!

## Next Steps


