# Structured Logging for the Standard Library

https://go.dev/blog/slog
https://pkg.go.dev/log/slog

Lots of packages do it:

`logrus`, `hclog`, `zap`, `apex`, `zerolog`, `xop`, ...

## The problem

If each package has its own logging, difficult to wrangle them to look the
same.

It's worth having it in the standard library.

## Architecture

`Logger` produces `Records` passed to `Handler`

## Levels

Levels are integers

Some have names, but room inbetween for your own

Info is zero.

(Same as open telemetry standard)

## Keys

Conflicting keys? Can be deduped by logger groups. So subsystems can have
their own values nested, and prevent conflicts.

You can also use `logger.With()` to A

## Performance

Logging can be in the inner loop.

History of Go structured logging:

* logrus
* `zap` at Uber
* `zerolog` is fastest but less popular

"Good enough to get the performance of zap"

## Four Steps to Faster Code

1. Know your use cases. Benefit of _a lot_ of open source code to analyze.

"Almost all calls had 5 or fewer attributes."

Also "only about 8 common attributes".

2. Write good benchmarks.

3. Design the right API.

`Logger.LogAttrs` takes `...Attr` rather than `...any`.

`Handler` is able to provide optimizations.

4. Avoid _allocation_.

"Allocation is the main determinant of performance"

* Make `Logger` a concrete type
* Record holds an array of Attrs, overflowing to a slice
* Value avoids some allocations that happens with `any`
* Internal buffers are pooled

## Process

April 2022 Expore

Discussion

Proposal

March 2023 Accepted

August 2023 Go 1.21 released

There had been an ongoing experimental implementation.

### Feature 1: Logger in Context

Controlversial.

Not integral to the proposal. Can add later.

### Feature 2: Alternating Keys and Values

Kept it.

"Go should be easy to write, light on the page"

You still can use explicit attributes if you want.

Also added a `vet` check for incorrect key-value.

## Next?

New code will start using it.

Existing log packages will add shims to `slog`.
