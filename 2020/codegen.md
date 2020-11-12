# Debugging Code Generation in Go

```
Source > Compiler > Linker > Binary
```

## Compiler

* Compiler
* AST
* SSA
* Machine Code

## Worked Example

Tiny package as an example. Adds `1+1` and prints. Inspecting the compiler.

* `go build -a` builds everything
* `go build -x` will print out tool invocations
* `go build -n` for a dry run
* `go build -work` shows work directory
* `-gcflags="-S"` for assembly
* `go tool objdump`
* `go tool nm`
* Disable optimizations with `-gcflags="-N"`
* View SSA with `GOSSAFUNC=main go build -a`
* Escape analysis: `-gcflags="-m"` or `-m=2` for more verbose
* Liveness analysis: `-gcflags="-live"` or `-live=2` for verbosity

## Inspecting the Compiler Itself

* `-gcflags="-bench=bench.out"` compiler benchmarks
* `-gcflags="-memprofile=profile.out"` to profile the compiler
* `-gcflags="-traceprofile=trace.out"` for an execution trace

