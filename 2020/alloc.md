# Evolving the Go Memory Manager's RAM and CPU Efficiency

Michael Knyszek

## Intro

How does Go manage OS-provided memory?

Goals, policy, design.

Talk about things allocator experts don't talk about much.

* Background
* Interacting with OS
* Managing Pages

## Background

Pages, smallest unit of memory management.

Virtual memory:

* layer on top of physical RAM.
* Every program has 64 bit virtual memory space.

Specialized hardware for virtual memory translation, specifically virtual to
physical translations.

So, pages are _fixed_ sizes. Usually 4KiB to 64KiB. Smallest unit that the OS
knows how to give out.

## Dynamic Memory Allocation

Allocate memory _at runtime_.

OS gives pages. We rarely have page-size things.

Layers:

* Go object allocator (ignored in this talk)
* Go page allocator
* Operating System
* Physical Memory

Assumptions that allocator needs:

* A "pool" of free pages
* To allocate contiguous blocks of pages
* Reuse partially-used pages
* To free pages back to the pool eagerly

## Lifecycle

"Free pages" pool. Allocate and return from the pool. Fill pool with new pages
from the OS.

Obtaining memory:

* Some syscall like `mmap`. Map virtual address space to some physical thing
  like RAM.
* `madvise` to say you _don't need the contents_ anymores

Freeing says you don't need contents anymore. But its still in your address
space, and you could touch it. Two schools of though:

* Unixy: touch it again and the OS will back it with real memory
* Windows: ask first

Results are mostly equivalent.

## Policy

Obtaining (simple):

* When we're out of memory
* What: contiguous

Returning ("an enigma"):

* Up to Go 1.11: return when unused for 5 minutes

Problem... large allocation request with fragmented heap. Requires asking OS
for more memory even though we don't really need to.

In Go 1.12: also return memory not every 5 minutes but also when we ask OS for
more memory.

But for performance reasons, we can't scan the whole heap, we need to _return
largest contiguous blocks of pages first_.

## Results

Tested this on a large Google service... ended up with unbounded fragmentation
and a virtual memory leak.

* Free pages organized in a tree... but only over a certain size.
* Simplified it... but the find operation was broken!

After the bugfix, it all worked as expected!

Should reduce chances of out-of-memory errors.

## Heap Spikes

Takes 5 minutes for a spike of allocation to take effect.

If it's bursty, and you get a spike more frequently than every 5 minutes, then
you'll _always_ have more memory allocated that you only rarely need.

Let's adjust the policy. When:

* Gradually at a fixed rate (not at a fixed rate)
* When we ask the OS for memory

What memory?

Policies, current: allocation "best-fit" with return policy "largest first"

Allocate: "Address-ordered first-fit" works just as well as "best-fit".
Return: highest address first.

## GC

Garbage collection happens when memory has grown to `live*GOGC/100.0`.

So the allocator considers this. Target 10% more than what the heap will grow
to (need some overhead).

## Results

In practice, this handles heap spikes much better!

Performance regressions:

* 3x increase in k8s API tail latency
* also to gRPC (15% regression in large message benchmark)

Regressions were surprising given the magnitude of the change. Turns out there
was something else going on, deep in the runtime.

## Managing Pages

Remember that tree? Protected by a lock.

That lock was collapsing!

* `runtime.mutex` optimized for the uncontended case, throughput increases
  with CPUs at first but then degrades with more paralellism. We call this
  "collapsing".
* `sync.Mutex` has code that retains performance when contended,by managing a
  queue of waiters.

`runtime.mutex` works on threads, `sync.Mutex` works on goroutines.

Microbenchmark for 1KiB allocation throughput demonstrates this bottleneck.

What can we do without rewriting `runtime.mutex`? Can we avoid grabbing the
lock at all?

Observation: most allocations are small.
Idea: process-local page cache.

CPUs don't like trees. What can we use instead. Bitmaps?

1 bit per page, keep pointer to the first free page.

* So we have a global bitmap.
* We want to cache some of it locally.
* Take the word that the pointer is mapped to.
* Mark it all used in the global map, move it to the processor local.

But: what about large allocations. Searching the bitmap is linear, rather than
the logarithmic tree we had before.

Summarize the tree: keep (start, max, end) summary for finding free pages more
easily. _We can build a tree from the summaries!_. So we can search for large
allocations with the tree.

But trees have problem right? This is different, don't need pointers we can do
indexing math.

## Results

Go 1.14 saw massive real life server benchmark improvements.

Also improvements in the microbenchmark.

## Another Bug?

Faster Allocation so more GC pressure

...

Fix: limit GC feedback loop.

## Conclusion

Like an onion:

* layered
* complicated
* sometimes makes you cry

Takeaway:

* Gradually returns highest-addressed memory
* Leaving
* In Go 1.14, manages pages in a summarized bitmap
* Scales better to many cores

More people should talk about this stuff!

_Most allocators_ manage pages the same way.
