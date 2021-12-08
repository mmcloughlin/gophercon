# Generics!

Landing finally in Go 1.18. Three additions:

* Type parameters
* More powerful interface specifications
* Type inference

## Type Parameters

Instantiation:

1. Substitute type arguments for type parameters
2. Check that type arguments implement constraints

You can instantiate without calling, like `min[float64]`.

It works for types as well as functions:

```go
type Tree[T interface{}] struct {
    Left, Right Tree[T]
}
```

## Type Sets

Value parameters `float64` are sets of values.

Type of type parameters defines sets of types. We call them `constraints`.

New stdlib package `constraints` with standard constraints.

```go
func min[T constraints.Ordered](x, y T) T {
    if x < y {
        return x
    }
    return y
}
```

Type sets are defined as interfaces.

Interfaces define method sets. If a type defines the required methods, it
implements the interface. Alternative view, interface defines _set of types_
with those methods.

Extended syntax so types can be explicitly added to interfaces.

```
interface {
    int|string|bool
}
```

For example:

```
type Ordered interface {
    Integer|Float|~string
}
```

The `~` is a new token added to Go. `~T` is all types with underlying type
`T`.

Purpose of a type set:

1. Type set of a constraint is the set of valid arguments
2. Operations permitted on types are allowed in the function

Constraint literals: they can be given directly, with syntactic sugar:

```
[S interface{~[]E}, E interface{}]
```
becomes
```
[S ~[]E, E any]
```

## Type Inference

For example, we can call `min` without type inference, as long as the type of
the arguments is known.

Exact details are complicated, using it is not.

Constrint type inference. Problem: suppose we define:

```
func Scale[E constraints.Integer](s []E, c E) []E
```

and then we call it with some derived type `type Point []int32`. Then it will
return `[]int32` not `Point`. How to fix that?

```
func Scale[S ~[]E, E constraints.Integer](s S, c E) S
```

Then it will do the right thing. How does this happen:

"Constraint Type Inference"

Used when one type has constraints defined of another type parameter. For
example, in the above `S` is defined in terms of another parameter `~[]E`.

In this case, `E` can be deduced if `S` is known.

## When to Use Generics

> Write code, not types.

When are type parameters useful?

**Containers.** Functions that work on slices, maps and other containers
without making assumptions about contained values.

**General-purpose data structures.** For example linked lists or binary tree.
Currently use interfaces. Now use generics.  For example:

```go
type Tree[T any] struct {
    cmp  func(T, T) bool
    root node[T]
}
```

Prefer to use functions, not methods, so that it can be used with basic types
like int, which we can't add methods to. "Easier to turn a method into a
function than add a method to a type"

**Function that "looks the same" for all types.** For example, a function

## When not to use generics?

**When just calling a method of the type argument.** Just use an interface.
We don't need to replace all uses of `io.Reader` with generics.

**When the implementation of a common method is different for each type**

## Summary

**Avoid boilerplace.**

Use generics when you're about to write the exact same code again.
