# High-Assurance Go Cryptography

"How we keep bugs out of the crypto standard library."

"Community effort"

Cryptography is special: **really needs to be no bugs**.

Almost all bugs have security impact.

Talk about techniques to improve robustness. More broadly applicable, even if
you're not working on cryptography.

* Memory safety
* Tests tests tests tests
* Fuzzing
* Safe APIs
* Readability, maintenance

## Memory Safety

No buffer overflows or use after free.

If you're using Go, you have memory safety.

Now let's talk about things that don't come for free...

## Testing

Libraries of test vectors.

Especially using open-source test vector libraries.

Wycheproof covers lots of edge cases in common implementations.

https://github.com/google/wycheproof

Cute **trick**: wycheproof repository fetched like a Go module, to leverage
caching. Even though it's not actually a Go project.

ed25519 safe migration: generated a large set of test vectors from the old
implementation, and continue to test that they agree.

Write a program to take a set of test vectors exercising edge cases, and
expand them into more test vectors that exercise edge cases _in combination_.

How do we know the tests cover what we want?

We have tests for the tests? Confirm we have the number of test vectors we
expect. Confirm we have test vectors with the properties we expect.

Another **trick**! Test the tests by running them on _known broken_ versions,
and confirm the tests catch known broken versions.

## Complex Protocols

Harder than testing single algorithms.

Test TLS by communicating with other external implementations, like OpenSSL.

Write helper functions to make spinning up OpenSSL clients easy, and make the
tests comprehensible.

What if OpenSSL isn't available?

Nice **trick**! Have a recording mode to record sessions from tests when
OpenSSL was available. Allows for _replay_. Under the hood, uses a wrapper
around `net.Conn` to intercept network connections.

Have `-update` flag (like golden tests) which update the tests data from real
OpenSSL and write the test data files.

## Nice Consequence

When testing is easy, you end up with a lot of them!

## `<foo>test` package pattern

A special test dedicated to making it easy to test the package `<foo>`.

For example, `acmetest` or `httptest`.

Libraries like this often contain realistic simulators or mocks.

Take a `*testing.T` in the mock, and fail the test if something bad happens.

## `age` testing

How do you have a safe API while also allowing yourself to construct _bad_
inputs for testing purposes?

Solution: write an internal `testkit` package that allows manipulating the
file format at a low level, so you can construct bad files with it.

The testkit package is used to write small go programs that output text files.

The text format of the vectors is easy to read.

Also there's no longer any dependency from the main library on the test vector
generator. Allows us to use other external packages for the generation.

Again... allowed us to add many many tests!

Also... the text files can be shared with other implementations of `age`.

"Good for age implementation, but also the age ecosystem"


## `testscript` package

"Little gem"

Exported version of internal package in stdlib.

Test scripts contain:

* How to setup the environment
* Files to make available
* Commands to execute
* Assertions about the output

"Such a good tool"

## Fuzzing

Let the computer generate the tests for you.

_Differential fuzzing._ Two implementations that are supposed to be the same.

A lot of this done by the `cryptofuzz` project.

_Weighted fuzzing._ "Randomly throw stuff at the wall." For things like
cryptography, this can be extremely bad.

We use special fuzzers guided towards values that are more likely to turn up
bugs than others.

"Weird limbs"

Not completely black box testsing. Help the fuzzer towards areas where we
think bugs might lurk.

This worked! Found test vectors that would never be found otherwise, but in
the weighted fuzzer it came up with `2^-18` tries.

## Safe Interfaces

"High assurance is not entirely about finding bugs"

Perhaps more important: writing software such that introducing bugs is harder.

"Software engineering is what happens when you add time and other programemrs"

Even if its used safely now, doesn't mean it always will be.

Refactoring the Elliptic Curve packages with a safer internal API.

## Complexity

We have a security track record that's better than many libraries.

Not because we are better engineers or test more.

It's because we target a _subset of functionality_ that's a lot smaller than
other comparible libraries.

**Manageable complexity!**

Allowed us to survive all the way from Adam Langly's libraries.

## "The Go Cryptography Principles"

Secure

Safe

Practical

Modern

.... In that order!

Performance is not on that slide. It's part of practical. But, it can be fast
enough. We don't need to lower assurance to make it faster.

"That gets to saying no..."

The Cryptography Principles are a tool we use to say no.

"No is temporary, yes is forever."

What about stuff already there...

## Deprecation

We deprecate things liberally. Not removed but a strong hint not to use it.

Deprecate with suggestions of where to go.

## Readability

There's an almost meme: cryptography is complex so it can be unreadable.

That's upside down.

> The most complex programs should be the most readable.

"Willing to take a performance hit to make things more readable."

Helps writers and reviewers.

## Maintenance

Maintenance is just another technique on par with the ones we have mentioned
so far.

Continuously put effort into codebases so the confidence we have in them
grows.

Add tests. Add fuzzing. Improve APIs.

And... Filippo is a full-time open source maintainer.

