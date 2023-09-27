# Balanced GC: A Copying Garbage Collector for Golang

Goal: optimize performance of microservices

Find: generational hypothesis holds in ByteDance

Balanced GC:

* Bump allocating pointerless objects in goroutine allocation buffers
* Use copying GC to manage allocation buffers
* Approximate 4%

## Go Ecosystem at ByteDance

Tens of thousands of microservies

Most written in Go

* Flexible and expressive grammar
* Efficient and scalable concurrent mechanisms
* Builtin GC

## Profiling Microservies

* Massive RPCs performaned on loosely-coupled microservices
* Communicating through JSON, Protobufs and Thrift
* Session based behavior
    - Accepting requrests
    - Marshal/Unmarshal
    - Performing specific business logic

**Generates lots of temporary objects**

## Optimizations

Note that most objects are small.

> 88% of objects are less than 128 bytes

Inspired by thread-local allocation buffer of JVM

Introduce Goroutine allocation buffers (GABs)

Bump allocating noscan objects in GABs

Object size threshold: 128B

## New Workflow

Have to make sure this is compatible with the existing allocator.

Each GAB is a large object allocated by the Go GC.

There are some live objects in the middle of the GAB.

**Solution: use a copying GC to compact live memory in the GAB.**

Use a different style of GC to manage objects with different life cycles.

## Technical Details

How to locate gaps in the GAB?

Objects aligned to 8 bytes. _Use bitmap to mark the begininng of objects._

...

Result: merge multiple small allocations into one large allocation.

Microbenchmarking shows large performance improvement.

### GC

Dead GABs can be reclaimed by Go GC as a whole.

GABs with live objects are retained.

GAB evacuation is performed if total size exceeds a threshold.

Estimate the size of GABs that could be allocated in the next GC cycle.
(Based on the configured next target trigger from `GOGC`.)

### Marking

Performed when the balanced GC evaluation is triggered

Piggybacks on concurrent GC marking.

Collecting the location of live GAB pointers in per-p queues to avoid
contention.

Computing the total size of live GAB objects.

No explicit boundaries between GAB objects. Go allows interior pointers.
_Leverage the bitmap to compute the size of objects._

End result is pointer queues for live objects in GAB

### Evacuation

Performed during mark termination

Processing pointer queues until empty

Live objects moved to "survivor gaps"

"Typical moving GC" with forwarding pointers.


## Pitfalls and Lessons

Challenges:

* Implementing a copying GC above mark-sweep GC is difficult
* Some code assumes objects are not moved
* In particular, the relation with the `map` type

Go maps:

* Consistency: hash code of the same object must be the same regardless of
  when it's computed
* So the hash code can be computed on demand
* Method: AES encoding of the object memory

This is a challenge for moving GC.

* If a gap object is moved, the pointer changes
* Therefore the hash code of memory containing a pointer could change

Fix:

* **Pinned** objects
* Pinned objects cannot be moved
* All GAB objects pointed to by map buckets are pinned

## Evaluation

Added a standalone runtime option to enable balanced GC.

Enabled balanced GC in many ByteDance microservices.

**4% improvement** on average

**10% perforance improvement** for good use cases

## Future Work

* Parallel evacuation to cut down evacuation time
* Supporting bump allocating scan objects (limited to noscan now)
* Concurrent evacuation
