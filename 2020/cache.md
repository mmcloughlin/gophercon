# Build and Test Caching in Go

## Make

Old, on every machine.

Make uses modification times of files.

https://github.com/kevinburke/make

Downsides to modification time:

* If you change the commands (like add `-race`) it doesn't know

Make: very easy, but has downsides.

## Bazel

Aims:

* Reproducible: describe environment completely
* Fast: don't rebuild things you don't have to

Very verbose and explicit.

`bazel-bin` contains all the artifacts. Note it generates `.x` from `.a`
files, which is the subset of the `.a` that you would expect to change when
the code changes (it's the cache key).

"Bazel is like an F1 car." Very powerful, but high startup cost.

## Go

### Build Caching

Outputs are `.a`.

Cache key: combination of anything that could affect the `.a` library.

```
key = func(GOOS, GOARCH, ..., files, ...)
```

Checksum used to compute cache key out of all these fields.

Use `GODEBUG=gocachehash=1` to see complilation logging about cache keys.

See `go env GOCACHE` for the cache location on disk.

Some entries are "action" entries which point to other entries.

### Test Caching

But... tests have side effects.

Files in testdata. Environment variables etc.

Go will monitor syscalls (open, getenv, stat, ...) and log them to the
"testlog".

Testlogs also kept in the build cache.

Now, `go test` will check the testlog and rerun the syscalls to check if they
all match. If so, no need to run the test again.

### CI

1) Load the cache from "go env GOCACHE"
2) Build binaries
3) Run tests
4) Save the contents of "go env GOCACHE"
