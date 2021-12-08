# Go Profiling and Observabilty from Scratch

## Memory management

Goroutine stacks (4KB+) + Heap

Free stack data is trivial: just adjust stack pointer.

Heap:

* Reachable from some in-use variable
* Unreachable "garbage"

Garbage collector periodocally cleans up unreachable.

Heap allocaiton expensive, can be 20% or more of CPU time:

* Reduce: avoid heap allocations, or use stack instead
* Reuse: reuse allocated objects
* Recycle: some GC work is inevitable, it's okay

Note: reducing heap allocations can speed up unreleated code.

## Profiling

**CPU Profiler:** How much time is goroutine on CPU. Sampling based.
Interrupts every 10ms, takes stack trace, keeps frequency count. Sample rate
set with `runtime.SetCPUProfileRate`, but recommend to not touch it.

Details: installs signal handler for SIGPROF. Invoked by setitimer syscall.
Handler stops all goroutines.

setitimer has issues, can't deliver more than 250 signals per second, biases
profile to underestimate CPU spikes. Go 1.18 patch fixes this by using
`timer_create`.

**Block Profiler.**

* Off-CPU time waiting on channels and mutexes.
* Not: sleep, io, syscalls, gc
* Data: cumulative contentions and delays

**Mutex Profiler.** Off-CPU time waiting on mutexes.

Mutex profile shows you which code is _doing the blocking_.

Block profile shows you what is _getting blocked_.

**Memory profile.** Cumulative allocs and in-use per stack trace.

Samples captured every 512KB of malloc, and when those objects are later
free'd.

**Goroutine profile.** Goroutine counts per stack trace.

"Stop the world" profile. High overhead with many goroutines. No sampling
mechanism.

Good for diagnosing gorountine leaks or hanging problems.

## Tracing

Recording timestamped events.

* Distributed tracing. Requests through multiple services.
* Runtime tracing. Go's builtin tracer.
* Tracing profiler. Tracing every function call.

Runtime tracer:

* Scheduler
* GC
* Contentions
* Syscalls
* ...

High overhead _firehose_. But fantastic way to track down latency. Chrome
tracer UI.

## Overheads

Setup: run workloads for a minute, with and without profilers enabled.

Caveats: hard to get right. Early sneak peak. Stats not great. Workloads
naive. But interesting enough to share.

**SQL Query Workload.** Minimal or zero overhead for all profilers.

**HTTP Hello World.** Block profiler some overhead. _High_ overhead for
tracer. Substantially higher for concurrency 1.

**JSON Workload.** Apparently makes it _faster_ with CPU profiler or tracer
enabled. What?

**Channel workload (synthetic).** _Massive_ overhead for tracing, high
overhead for block profile.

Various error sources, but still gives you an idea.

## Metrics

https://pkg.go.dev/runtime/metrics

## Third-party

* linux perf
* bpftrace
* delve
* fgprof (author's tool)

