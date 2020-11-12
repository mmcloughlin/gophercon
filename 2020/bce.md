# Common patterns for Bounds Check Elimination

## Intro

Slice access produces `if` statement to check index is less than size.

Can be painful for performance sensitive applications.

Do we really need it? C/C++ does not, but the cost of mistakes can be very
high. Buffer overflows, segfaults.

Tradeoff between performance and security. Can we have our cake and eat it
too?

## Compiler

In this case, no bounds check:

```
for i := 0; i < len(a); i++ {
    _ = a[i]
}
```

In this case, there _are_ bounds check:

```
for i := 0; i < 5; i++ {
    _ = a[i]
}
```

Check if bounds checks are happening:

```
go build -gcflags=”-d=ssa/check_bce/debug=1” foo.go
```

Assembly:

* It's an `if` comparison and a `runtime.panicindex` call

## Common Pattersn

1. **Loop upper bound is known**. May need to add a _dummy access_ `_ =
   b[n-1]` before the loop so the compiler does the bounds check once. That
   is, _hoist_ the bounds check.
2. **Contiguous slice access**. Again, add a dummy bounds check at the top.
3. **Offset from a base index**. May need to reslice.
4. **Multiple slices with known upper bound**. Use dummy accesses.
5. **Slice range can be derived** for example if the access is a byte, you can
   do a _dummy_ reslice to h[:256] to help.

## Demo

Levenstein distance. 25% improvement.

## Keep in Mind

Only to squeeze out last drop of improvement.

Use benchmarks to confirm.

Check against latest Go version, they might not be necessary any more.
