# Pardon the Interruption: Loop Preemption in Go 1.14

## Problem

GC latency driven down over time.

One major problem. Tight loops.

To stop the world, need to "get the attempt to" of all goroutines.

Sometimes goroutines give up control. "Voluntary Preemption"

In Go 1.0, only voluntary.

In Go 1.2, function call preemption. At the start of functions, check of
something is pre-emption. Check if need to preempt.

Normally... lots of function calls. But what if some are computationally
intensive. They won't reach any preemption points.

Example: trace from Twitch. 48 core CPU, 5ms gaps when massive base64 decode
blocks.

## Nasty Cases

Change in sched package (inlining mutex fast paths) made some existing code
deadlock, since a (naive) spinlock does not have any function calls.

## Problems

- Loops cause scheduling latency
- Reduces throughput
- Can even cause scheduler deadlocks

Plus, _very difficult_ to fix.

In Go 1.14 landed async preemption.

* 25 microsecond pre-emtpion bound
* 0 performance overhead
* Tiny 

## False Starts

### 1. Pre-empt Back Edges

Preempt back edges... 8% performance degredation. Inserting instructions into
_every_ loop. Painful cost.

Want zero cost.

### 2. Forward Simulation

"Forward simulation", like other languages.

* Break into goroutine
* Single step to back edge
* Pre-empt path

How do we single step?

* Write emulator? Are you insane?
* Single-step the program?

Act like a debugger? But this 

### 3. Use Interrupts, like an OS

Well, from userspace we need to use signals instead.

Swap CPU state.

But... the garbage collector. We need to know where the pointers are in the
CPU state.

Compiler produces stack maps.

Previously, only needed stack maps at function entry points.

Now we need them at the start of loops too.

Can we produce stack maps for _every_ instruction?

Massive amount of metadata +10% and _impossible_ to test.

### Summary

* Explicit loop preemption is too costly
* Signal-based is good but too much

## Asynchronous Preemption

GC:

* Precise: know exactly what pointers are
* Conservative: if it might be a pointer, assume it is

Switch to a Conservative GC. So:

* Send signal to goroutine
* Examine where it stopped
* Conservatively scan function
* Precisely scan rest of stack

Still have to deal with "unsafe points":



## Implementation

Chose to use SIGURG.

* Basically not used
* Safe to receive multiple SIGURG
* Okay to send many
* GDB does not stop for SIGURG

So... in the rare case where someone is using SIGURG, it will be okay that Go
is using it too.

Thread kicked into `runtime.sighandler`:

* So hard to write
* Unclear what's safe to be done
* Do the _minimum_ amount in the handler
* In particular, resume the goroutine in `runtime.asyncPreempt`

## Summary

* Preempt anywhere
* Metadata-less conservative GC
* Zero-cost signal preemption
* Requires non-moving GC

Go ended up in a unique point of the design space relative to other languages.

* Low latency
* High throughput
* Greater stability
