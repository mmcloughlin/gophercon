# Building a High-Performance Concurrent Map in Go

If we use the runtime built-in map concurrently, it can panic.

How to build a concurrent-safe map?

* `sync.Map`
* `map[K]V` guarded by `sync.RWMutex`

Let's do a benchmark.

* `sync.Map` is faster in the load only case. Designed to solve the problem of
  cache contention.
* For mixed read-write, the map and mutex is faster.

Let's look at `RWMap`:

* Only one write operation can be executed at a time
* Multiple read operations concurrently

Can we make the write operation only block part of the map? For example only
the bucket it maps to.

What about a "Shared map": multiple read-write mutex maps.

* How many maps? Usuaully recommend one per CPU core.
* How to shard? Use hash function.

Benchmark: faster in all cases, but requires more memory.

In reality, a perfect hash function doesn't exist. Still get cases where
different operations go to the same submap.

What we did was reduce the size of the locked region and got a 2x performance
improvement.

What if we take it to the extreme? Each submap contains one item.

... linked list.
Each node has a lock.
All write operations must acquire locks.
Read operations are lock-free.
Use atomic reads, and mutux plus atomic writes.

Ordered, concurrent safe linked list.
Accessing the linked list is `O(n)`.

Skip list is a bunch of linked lists:
* Bottom list contains everything
* Upper layers exist to speed up search

Replace each linked list in the skiplist with a concurrent-safe linked list.

**Skipmap**: 20x faster in common case, and faster in all cases.

Skipmap is `O(log(n))`, `sync.Map` is `O(1)`. The sync map may be faster in
lower concurrency environments.

Summary: reduce size of locked regions
* RW mutex map -> entire map
* Shared map -> one sub map
* Skipmap -> few items
