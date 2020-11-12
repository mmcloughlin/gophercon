## defer

Keyword.

Runs on exit or panic.

Can be called in `if` or `for`.

## Other Languages

RAII C++

- Statically analyzable.
- Only applies to non-Garbage collected languages.

`try-finally` Python, Java, Javascript, ...

- Statically analyzable

Go finalizer:

- `runtime.SetFinalizer`
- Run before destruction
- No guarantee before `panic` or `exit`

## Pro and Con

Pro:

* Improves _maintainability_: more likely to stay
* Improves _robustness_: happens even on `panic`s
* Improves _clarity_: cleanup near construction
* Good for _conditional_ cleanup

Con:

* Completely dynamic
* Straigtforward implemenation requires runtime
* But simple cases can be known statically

## Implementation in Go 1.13

* Eval function pointer
* Eval arguments
* Push to defer chain (`runtime.deferproc`)

Then `runtime.deferproc` before all returns, which processes the defer chain
in LIFO order.

Now, `runtime.gopanic` also processes the defer chain.

## Go 1.14

Make _direct_ calls directly in simple cases.

* Only applicable for functions where no defer statement is in a loop.
* Set bitmap of which defers were activated.
* Call these "open coded" defers.

## Tradeoffs

Sicne there is no defer record anymore, extend "funcdata" records that
describes where the stack slots are.

Need an extra stub of code to handle the recover case.

* Scan the stack for frames that use open-coded defers.
* Insert record for each frame in the defer chain
* Iterate through the defer chain in usual LIFO order
    - for open coded frames load the funcdata
    - load deferbits execute all _active_ defers

# Results

Now very close to the cost of a pure function call.

* Improvement in cgo calls from C back to Go
* Improvement in `crypto`

For future, we can have both performance and maintainability.

# Conclusion

* Important language feature
* Optimized greatly in recent releases
* Go should always be used when it makes sense
