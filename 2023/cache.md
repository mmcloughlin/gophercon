# Building a Highly Concurrent Cache in Go: A Hitchhiker's Guide

...

Caching post data. Layer in front of Redis.

Cachable per-K8s pod?

Basic cache: `map[string]string` with a `sync.RWMutex`

"Excellent API for general-purpose cache, but we don't want a general purpose
cache."

For example:

* We often need to look up 1000s of keys per call
* So, for example, change the API to fetch multiple keys per mutex acquisition

Cache replacement policies:

* Belady's is theoretical but perfect: use furthest in the future
* LRU
* LFU
* ...

How does LFU change the cache implementation?

* Maintain a heap by frequency count
* Update heap on access
* Pop from heap on eviction

How to decide the eviction policy? Benchmarks!

Collect real traces as an input to the benchmark tool.

Benchmark therefore simulates based on real production traces.

Use `benchstat`!

Seems to suggest that LRU comfortably beats LFU.

How about _hybrid_ approaches?

## LRFU/DLFU

Similar to LFU in implementation, with a heap for the eviction priority.

The score is a function of the time and frequency.

Lambda parameter controls how

DLFU:

* LRFU decay is exponential but math.Pow expensive
* Exponential decay can be approximated
* Exponentially grow the reference increamement

Results:

* Better hit ratio
* But worse read/write performance

Spending a lot of time in locks.

Moved to more granualr lock acquisition.

## Production

Initially had to revert rollout. Spiking goroutines.

More phased rollout:

* Incremenetally increase proportion of requests using the cache
* Context cancelation: timeout cache accesses.

## Beyond `sync.Mutex`

Revisit the `sync.Map` documentation!

https://pkg.go.dev/sync#Map

Specialized for two cases:

> (1) when the entry for a given key is only ever written once but read many
> times, as in caches that only grow, or (2) when multiple goroutines read,
> write, and overwrite entries for disjoint sets of keys. In these two cases,
> use of a Map may significantly reduce lock contention compared to a Go map
> paired with a separate Mutex or RWMutex.

Not suitable for this application, but is any of it applicable.

## Cacheline Hash Map

Cache coherence protocol ensures each CPU core has a consistent view of the
same data.

But it can impact application performance.

The `xsync` package is a concurrent hash table based map, where hash buckets
are cacheline sized.

https://github.com/puzpuzpuz/xsync

Leverage this and:

* Switch to a "trimmer" goroutine that periodically performs evictions by
  sorting all entries
* ...

## Summary

"Implementing your own data structure can have a lot of upsides"

Different cache replacement policies.

DLFU cache

How to write benchmarks, and use the Go profiler

Cache coherency protocol can affect performance

"No such thing as lock free when multiple processors are involved"

Performance can be improved with atomics and lock-free techniques
