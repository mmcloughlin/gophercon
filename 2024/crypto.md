# The Go Cryptography State of the Union

It's been an exciting year for Go cryptography and security.

## Post-Quantum Crypto

> The new post-quantum algorithms, the ones that can’t be broken by quantum computers, are finally here and ready to go in crypto/tls. We’ll look at how we implemented them for testability and safety, and why rolling them out now is important.

Key exchange. Store now decrypt later attacks.
Post-quantum algorithms exist.
PQ is fast, but large. PQ key exchange takes ~1KB vs a few bytes.

It's also new. Not as sure about security.
Handle it with hybrids.

Kyber. Standardized by NIST.
`crypto/internal/mlkem768`

> Agressively optimized for readability.

Exhaustive tests.  Negative test vectors. Unlucky test vectors.  Test vectors
shared through C2SP.

Minimized allocs, zero alloc rewrite of sha3.

End result: same ballpark as X25519. **No assembly!**

Go 1.23 draft available in `crypto/tls`. Only in default config.

Go 1.24 hoping for NIST final draft.

## TLS

Crypto defaults since Go 1.23 give:

* Minimum TLS 1.2
* ECDHE suites
* No `3DES`

Goal is that the defaults will move forward with each release.
Environment variables can be used for old behavior.

Go 1.23: Encrypted Client Hello

Maybe in Go 1.24: Kernel TLS. Linux finished Kernel TLS 1.3 in 2022.

## Random

> One of the classic security foot-guns, using math/rand instead of crypto/rand, is now disarmed: the global math/rand and math/rand/v2 generator are now cryptographically secure. We’ll see why this was so dangerous and how we mitigated the risk while respecting the Compatibility Promise.

Accidental use of `math/rand` is too easy. Before Go 1.22 there is no secure
way to use `math/rand`.

Now `math/rand/v2` uses `ChaCha8Rand` a reduced round ChaCha for the default
random generator.

Reduced round `ChaCha8Rand` is standardized in C2SP:
https://c2sp.org/chacha8rand.

You should still use `crypto/rand`! But the risk of mistakes is lower.

Can `crypto/rand` ever fail? Realistically, no. So, in Go 1.23 will now panic.

## More...

> Past releases brought a new package, crypto/ecdh, large rewrites of the elliptic curve and RSA backends to be safer and constant time, and a number of deprecations. The final goal was to move math/big outside of the security perimeter. We succeeded! However, the new RSA backend was slower. It is no more! We’ll talk about the performance optimizations that went into that.

## SSH

> x/crypto/ssh was one of the least maintained packages in the library. Over the last year, it got a much-needed refresh from Nicola Murino, including new recent privacy features, protocol vulnerability fixes, and compatibility work.

## Next

> Speaking of x/crypto… did you ever wonder why some packages were in x/crypto and some in the standard library? No good reason! We decided to move all the good ones to the stdlib and deprecated or froze the rest. We’ll go over how and why we picked each package.

`x/crypto`: deprecating and migrating to standard library.

AEAD: new AEAD APIs. Maybe `XAES-256-GCM` with automatic nonces. To do this we
need large enough nonces that they can be safely generated randomly.

`runtime/secret`: what if you need to safely zero out secret key material.
Thought that language support for this was too brittle. Proposal converged at
`secret.Do` API that runs a callback and then zeros out the entire goroutine
stack. "The runtime is in on it." It was worth waiting to evolve this API.

> No is temporary, yes is forever -- Solomon Hykes

Constant time code: Apple DIT bit. We will have a `constanttime.Do` API much
like the `secret.Do` API. Goal to establish an "understanding with the CPU".

### FIPS 140

Finalizing plans on how to do it. Goal for a much smoother experience than.

## Looking Back

Assessing severity of vulns in stdlib is hard. Hard to measure because we
don't know how they are used.

Pattern of moving towards less scary vulnerabilities.

## Maintainership

Next step... Geomys.
