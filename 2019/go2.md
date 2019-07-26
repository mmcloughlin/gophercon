On the Path to Go 2
Russ Cox


Go Dev Process

    Experiment
    Simplify


Four Rs

    Reshaping
    Redefining
    Removing
    Restricting


Reshape

    Simplify overall

    append

        Early days, people write `append` over and over again

        Adding append made Go more complex, but made Go simpler overall


    go command

        Used to be *many* tools


Redefine

    Originally append only worked with []byte

    Redefined to work on strings as well


Remove

    One less thing to learn, fix bugs, maintain, cognitive load

    Can still be simpler


Restrict

    eg. source code must be UTF-8
        gofmt


Problem

    TOO SIMPLE

    When we shipped Go 1 it was for experimentation.

    Focussing on simplifications without language changes.

        1.5 concurrent garbage collector
        1.13 time to think about real language changes


Go 2

    Error handling
    Generics
    Dependencies
    Tooling


Errors

    Simplification:

    - Error values
    - Error syntax


    Path to error values

    - int64 error codes
    - then error string
    - then os.Error interface with String()
    - then builtin error type name Error() (Roger Peppe!)


Errors examples

    - syscall errno
    - higher level SyscallError
    - net.Error with many context

    It's POWERFUL to be able to expand the error interface.

    Errors *are* values.


Many error implementations

    github.com/pkg/errors and others...

    But, all slightly different.

    So, worth adding to the stdlib.


    errors can implement Unwrap() method.

    errors.Unwrap() helper.

    errors.Is implemenets the common unwrapping loop, looking for an error
    with some property.

    Formatter interface abandonned (not simple enough)


Error Syntax

    Previously: check and handle blocks

    "check like a quick way to write the if statement"

    "handle like a defer"

    handle overlapped too much with defer

    renamed check to `try`

    Simplify code but make other things more complex.

    Not clear the overall result would be simpler.


Generics

    Type parameters.

    Data structures.

    Four abandonned experiments so far!

    Hopeful that we're headed in the right direction.

    Ian Taylor presenting later in the conference.


Dependency Management

    goinstall experiment

    Then GOROOT -> GOPATH

    More places for go code, but simplified overall

    goinstall always downloaded latest copy

    Became "go get"

    Encouraged backwards compatibility

    Don't make breaking changes


Versions

    goven
    godep
    glide
    gb


Go Vendoring

    Lost: package uniqueness.

    (Parallel with try: we were relying on the visible return statement in
    ways we didn't realize until we considered removing it.)

    Go dependency working group.

    dep aimed to replace all the existing models.

    "Import compatibility rule"


Modules

    Group of packages are a module

    Integrated with go command

    End of GOPATH.

    As you switch projects, versions of dependencies change.

    Developer tools use x/tools/go/packages


Next

    1. Errors
    2. Modules
    3. Generics


Tools

    gopls: go please

    Next: gofix, govet, ...

