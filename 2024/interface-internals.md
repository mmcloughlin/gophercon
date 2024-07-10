# Interface Internals

How does Go do a multiply? "All the work is done by the hardware." Go just has
to tell the hardware to do it.

Interfaces are different. Go compiler has to decide how to do that. "We have
to figure out how to do this ourselves"

How do we make an interface call happen? For example `c.Close()` on an
`io.Closer`.

1. Find the contained type
1. Get list of methods
1. Find the one with the right name
1. Get its location in the binary
1. Jump to it

No magic instruction. How many instructions will it take? There's a fair
amount of work to do.

In actuality, we can do `c.Close()` in three instructions.

What are interfaces? "Weird dynamic things in a static language."

"Contract between the things you put in them, and the things you can do with
them."

Interface-typed variables can contain any type that has the methods listed in
the interface.

"Bridge gap between static and dynamic"

Static:
* compile time
* fixed type
* interface type

Dynamic:
* run time
* type can change
* value can change
* non-interface "concrete" type

How does this all work?

Conceptually interfaces are:

* concrete non-interface type
* value of that type

## Representation

How do we represent that at runtime? An interface is:

```go
type eface struct {
    _type   *_type
    data    unsafe.Pointer
}
```

### Data Pointer

To store value in interface, we allocate on the heap and then store pointer in
the interface value.

Therefore: assigning to interfaces in hot paths is a bad idea.

_Optimization:_ if your data is already a pointer, just use that as-is.

### Type Information

"Pointer to type descriptor."

Runtime type descriptors:

* Size
* Pointer fields
* String repr.
* How to do equality
* **List of methods**
* Read-only section of binary

For this talk, we need the list of methods.

Assigning to a interface value is therefore:

* Data pointer assignment
* Type descriptor pointer: "just a constant pointer to data section of binary"

Likewise, checking whether an interface is a certain type is a equality check
against the constant pointer to the type descriptor.

Pointer to the type descriptor treated like type ID.

"This only works if type descriptors are unique."

Compare against nil is also simple. `nil` is represented by the zero type
descriptor pointer. So `err != nil` checks are just single assembly
instruction, even if they are verbose in code.

## Method Tables

We know the contained type. What about the next steps? Still need to:

* Get list of methods
* Find the one with the right name
* Get its location in the binary
* Jump to it

Does the type descriptor have what we need? No.
We precompute information to make the interface calls more efficient.
Intermediate object is an "interface table" or `itab`.
The type descriptor is now two hops away.

So we actually have two different types of interfaces now:

* `eface` "empty interfaces" point to type descriptor
* `iface` points to the interface table instead

What is the itab?

* Type descriptor
* Interface type (useful for debugging)
* "method table": `fun` table, one PC pointer for every method

It's all built statically by the compiler and placed in the data section.

## Calls

"Reach into the method table and jump to it"

What does the compiler see?

```go
var c io.Closer
err := c.Close()
```

Knows the representation of `c`. Knows the itab layout.

Becomes:

```
pc := c.tab.fun[0]  // MOVD 24(R0), R2
receiver: c.data    // MOVD R1, R0
jump to pc          // CALL (R2)
```

"24 is just offset of fun[0] in the itab"

## Wrap-up

Squeezed performance, but adds complexity.

Mostly itabs are precomputed, but itabs sometimes need to be computed at
runtime.

This happens with runtime interface to interface conversion: for example
`io.Reader` to `io.ReadWriter` with more methods.

1. Check cache to see if we've made the itab before
1. Obtain list of all methods of concrete type
1. ...

In Go 1.22 an extra caching layer was added. Per-callsite cache.

## Conclusion

Interfaces are not magic, they are just good engineering

> Don't you just love the smell of assembly in the morning
Johnny Boursiquot

