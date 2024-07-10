# Who Tests the Tests?

Tests are our insurance policy. Code does what it's supposed to do.
Code doesn't do what it's not supposed to do.

Tests enable us to "go wild". They guarantee we didn't "cross the line".

Tests are also _design tools_.  First clients of our code.

Strong correlation between code that is **easy to test**, and code that's
**easy to read/maintain**. "... which I also call 'good code'"

"First opportunity to revisit the design."

## Measuring Tests

Two common metrics in the literature.

* Code coverage: percent code touched by tests.
* Test coverage: percent of functionality tested. "A bit more abstract"

Go compiler makes this super easy: `go test -cover` or integration test
coverage with `go build -cover`.

It's so easy to get this number that companies start setting goals. They start
setting limits in CI.

Goodhart's Law:
> A metric that becomes a goal stops being a good metric

Forcing coverage as a metric warps the goal. It's not about that insurance
policy anymore. It's about meeting the limit. Incentivizes you to write tests
you wouldn't otherwise write.

"Code coverage is very easy to fake."

You can get coverage without asserting anything about the results. You can see
this easily in single examples, but what if you have 1000 tests? How do you
know whether the tests are good?

## Mutation Testing

Apply mutations to the code, for example:

* Reversing operand order
* Negate conditional
* ...

Testing mutants:

* If fail, we way the mutant was _killed_
* If pass, we say the mutant _survived_

Quality of the test suite is measured by how many mutants it kills.

Demo: divide function with test case for 1/1. Mutant replaces `/` with `*` but
the test still passes.

## Mutations in Go

AST packages make it "easy".

Demo of using the
[`x/tools/ast/astutil`](https://pkg.go.dev/golang.org/x/tools/go/ast/astutil)
package to mutate if conditions and change divisors with multiply.

Go compiler supports an `-overlay` option that takes a JSON file specifying
replacement files.

```
> go help build | grep overlay
	-overlay file
		read a JSON config file that provides an overlay for build operations.
		exist if its backing file path is empty. Support for the -overlay flag
		included from, and overlays will not appear when binaries and tests are
```

Mutation testing tool produces an overlay file pointing at mutant files.

"Finally we have something that looks like a mutation tool."

## Mutation Test Tools

**Gremlins:** https://github.com/go-gremlins/gremlins

There are others, but probably gremlins is the one to converge on.

## Takeaways

* Write tests for the **right reasons**.
* Choose test inputs wisely: use table driven tests
* One mitation alone is not enough to determine if a test is good or bad
* Performing all possible mutations will be expensive. But, so is fuzzing.
* Mutation testing in Go is not mature, but **we can make it happen**
