# Reliably Absorbing a Go Release: Learnings from the Kubernetes Community

"Knowledge is the Dual of Possibility"

> "With a sufficient number of users of an API,
> it does not matter what you promise in the contract:
> all observable behaviors of your system
> will be depended on by somebody."

https://www.hyrumslaw.com/

With enough users it becomes very difficult to know whether you'll break
anything.

What does absorbing a Go release mean for K8s?

1. **CI**: Making sure tests pass is the easy part
2. **Users:** Ensuring your users won't break when they go Go update

Preparing:

1. Guaging the surface area of what can break?
2. What's the best way to mitigate a breaking change
3. Undesrsatand how the release and support cycles of Go align with project
   release cycles
4. Help users recocile with default Go behavior
5. Actually absorbing the release.

## Surface Area

Kubernetes is 2.2 million lines of Go code and about 240 dependencies.

Categories:

* Static analysis tooling
* Dependency management tooling
* Management tooling

## Ways things can break

* Code dependencies can break
* Your code can break
* Static analysis tooling can break
* The _runtime behavior_ of existing programs can change

"A release is only backwards compatible as its least backwards compatible
change"

## Mitigation

Might need small or large changes, but if you are in control that's the easy
case.

But there might be problems in dependencies, or even Go itself. In which case
you don't have control over it any more.

> Start testing early!

Some K8S tests even run on Go tip.

Testing early gives enough _soak time_ in CI.

## Release Cycles

12 month window for Go
14 month for Kubernetes

Result: Kubernetes versions that are supported but on unsupported versions of
Go

Rules about patch releases: no _destabilizing changes_.

Example problems:

* Breaking stdlib changes without sufficiently long `GODEBUG` opt-out. For
  example `GODEBUG` `tls13` happened too quickly.
* ...

New Go policy:

* `GODEBUG` setting will be retained for a minimum of 2 years.
* `GODEBUG` defaults are configured to match the Go version listed in the main
  packages's `go.mod` file
* A program can change individual settings with `//go:debug` directives, so we
  can override settings without polluting user's runtime environment

## Tracking

`kubectl` metrics for `GODEBUG` flags that are being relied on

Since Go 1.21 this is exported in `runtime/metrics`.

...
