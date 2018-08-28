The Future of Go

TL;DR;

  - Go 2 isn't going to be Python 3
  - Seeking community involvement on problems with proven significance in the
    REAL WORLD
  - Massive emphasis on backwards compatibility, but open to 3 major changes

Beginning:

  - Rapid development from 2009 to 2012 when Go 1 was announced.
  - API stabilized
  - Focus shifted to working with Go for real things
  - Reimplementing almost all of the backend
  - Focus on performance
  - Now its time to look ahead

Goal: Scale

  - Scale of system, deployment
  - Scale of codebase, development

  Fix the ways Go fails to scale

Go 2's constraint

  Existing code!

  Want to avoid Python 3.

  Changes to Go 1 must be worth it!

  Go 1 code *must* continue to work. Go 1 and 2 interoperate.

What can we do?

  Maybe three major changes?

  Have to consider the now "formal" process of Go development. Initially
  happened in adjacent offices. Now it has to happen in a distributed way.

What problems are worth fixing?

  Need to describe a problem and its *significance*

  Leap seconds:

    For example, timing something. Using time package doesn't work during leap
    seconds. Proposed providing access to monotonic clock.

    Initially decided not important enough. Waited.

    Cloudflare broke during a leap second, also wrote a well thought out blog
    post explaining the significance.

    Now the problem has been fixed, without an API chance. Will be in Go 1.9.

  Type aliases:

    Always considered, as they are elegant concert.

    It took much longer to prove the value for migrations in large code bases.

  Difficult but essential to explain a problem in a way that someone working
  in another environment can understand.

  Emphasis on REAL WORLD EXAMPLES.

  Encourage users to write "experience reports".

Do we actually need to change the language?

  - Could use libraries. For example `math/bits`
  - Or tooling? For example `go -race`

  Not all problems best solved with the language!

How would we ship Go 2?

  Backwards compatible parts first

  Encourage us to make backwards compatible changes first.

  Continue through Go 1.10, ..., 1.20, ...

  WE WILL BRING GO 1 ALONG


