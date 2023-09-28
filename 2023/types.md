# Everything You Always Wanted to Know About Type Inference

Automatically deduce the type of an expression without explicit annotations.A

Removes repetition or "stutters"

Generic Code potentially requires many more explicit types in Go code, unless
we do something about it.

We want generic code to look exactly like non-generic code.

Type inference as a form of type pattern matching

Example:

```
func Sort[S ~[]E, E cmp.Ordered](x S)

type List []int
var list List
Sort(list)
```

we can infer that `S` is `List`, and `under(S)` is `[]E`, so we can infer `E=int`.

Leads to **type equations**.

Reformulate the inference problems as a problem of solving type equations.

Types of relations

* Identical
* _Assignable_ to
* Satisifes _constraint_

Sources of type equations:

* Each explicit type argument provides a trivial type equation
* Each function argument (assignment) provides a type equation
* Each type constraint

## Solving Type Equations

Solving type equations is type unification.

... if type parameters P and Q are matched, then they are unified, since they
represent the same type.

...

## Other type relations

Type unification solves type inference for identical types.

What about assignability and constraints.

Note that type inference only infers types, it does not instantiate functions
or invoke them.

So there are steps after type inference that can catch errors in type
inference, just the error message might be different.

With that in mind, we _simplify_ some of the rules. So use simplified rules
for assignability, and simplified rules for constraint satisfaction.

## Untyped Constants

Type parameters always take precendence.

Untyped constant only considered if the type parameter is not specific, for
example `any`.

In this cases, falls back to default types for untyped constants.

Where multiple constants apply to a type, there is a priority.

## Special Cases

...

## What's Missing

Type inference for type instatiations

Recursive type parameter declarations
