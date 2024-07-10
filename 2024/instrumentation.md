# Advanced Code Instrumentation Techniques for High-Performance Trading Systems

Heisenburg: you can observe a system, but be prepared for it to act like it
knows you're watching.

If we are not careful, we may affect the system we're trying to measure.

In a performance critical trading system, they observed 35% overhead.

Trading has ultra low latency requirements.
Use `statsd` for monitoring.

...

## Inefficiencies

Inefficiencies in metrics collection:

* statsd tags require string slice allocation
* ... and often the tags themselves are inefficiently created with
  concatenation or formatting operations

Replace with a "metrics handle": an ID referring to metric metadata. Create
the metadata once at registration time and refer to it later.

## Heavy Locking

Single map used to store all metric types.

Note: read and write phases. Writing only at registration time.

Solution is an immutible map. After write phase, no locking is required.

## Suboptimal Buffer Pool

Uses a Go channel internally. Replaced channel with a disruptor.

Disruptor: one writer multiple readers. Ring buffer.
