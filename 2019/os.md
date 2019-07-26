Contributing to the os Package: How Deep Do You Go?
Oliver Stenbom


The journey from a bug report to a Go contribution.

"The Contribution Experience"

One of many experiences. Set you up for contribition for success.


Bug Report

    Long enough file paths could not be removed.

    > 4096

    The problem was in Go.

    Go's os.RemoveAll()

    Actually escalated to a CVE, since you can DoS by creating many
    undestroyable containers.


The Solution

    exec `rm -Rf`

    Simple solution.

    What if `rm` doesn't exist.


Where's the actual problem?

    100 lines 

    It's platform independent.

    Failed at `Lstat`... affected multiple packages.

    Variants: Openat()..., *at(), ...

    - Directory passed as a file descriptor.


Fix?

    Use the *at() variants.

    Copy over the recursive RemoveAll() function and modify it to use them.



Hurdles for Contributing

    Golang test suite

    Cross-Platform Compatibility

        Previous RemoveAll() was platform independent

        Use the general one if you can. Make it more specific later.

        Use build flags

    Non-existent syscalls

        *at() not in core Golang

        They're in /x/

        Have to implement them in Go for every unix-like OS


    Slow feedback pace

    Freezing deadlines

    High standards!

    Didn't turn out as expected


But...

    Pleasure to work with the Go team

    Didn't matter that I was a rookie on the other side of the planet!

    Learned:

    - os
    - unix system calls
    - High quality Go


Thank You

    Thanks to the Go contributors!

