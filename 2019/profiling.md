Two Go Programs, Three Different Profiling Techniques, in 50 Minutes
Dave Cheney


All Live Coding!

3 types of profiling
2 types of programs


Word Count

    Count number of words in some text

    1.2MB of text

    Approx 2sec
    wc -w was near instantaneous


CPU Profile

    go tool pprof -http=:5050 cpu.prof

    Loads of time in syscall.

    Also shows time in runtime. Substantial time in syscalls causing thread
    switching overheads.


Buffering

    Improved by an order of magnitude.

    Now very quick. But the profile shows us mallocgc.

    Maybe we should take a memory profile?


Memory

    Previously program was allocating temporary bytes for every read.

    Massive gain by reusing buffers.


Mandlebrot

    Take a trace profile.

    Single threaded. Speed it up by using multiple CPUs.

    - One goroutine per pixel?
    - Too many goroutines! Scheduler overhead.
    - One goroutine per row... much better
