# Automatically Instrument Your Go Source Code with Orchestrion

...

Java examples. `@WithSpan`. Similar in Java.

In many langauges, it's possible to modify even at runtime.

"Easily and non-intrusively add instrumentation"

But what makes Go great? No magic. Also no annotations.

So Go is great, but it's not easy to implement this auto-instrumentation
magic like other language runtimes can.

"You must compile trace code into your Go program"

"Certainly in the spirit of nothing hidden"

"Maybe we can borrow just a bit of magic"

What about eBPF? "A bit scary to use in production"

At Datadog, implemented Orchestrion.

Automatically adds spans to source code.

## Requirements

Instrument registered `net/http` handlers.

...

Configure which type of instrumentation is added.

Also want to be able to remove it.

## Parse Tree

Go parser and AST.

Problem of comments.

Use `dave/dst` instead!

## How to mark functions?

Use custom comment directives `//dd:`.

## How to cleanup?

Leave marker comments around.

`//dd:startinstrument` and `//dd:endinstrument`

This also prevents re-instrumentation?

## Technique

Showed how to modify the AST to insert instrumentation code.

Also: insert code that wraps HTTP handlers.

"Not hard, just a bit tedious"

## Backend Selection

"All problems in computer science can be solvied by another level of
indirection."

Use a variable set with the linker.

## toolexec

Oooh a nice **trick**, use `-toolexec` to run orchestrion as a preprocessor.

So you never see it in your program at all!

When run as a toolexec tool it receives the compile command.

* Check to see if you've been invoked with a compile command
* Create a temporary directory
* Look for all the `.go` files in command line
* Modify them and write to temporary files, then modify the comamnd line
* Invoke the modified

Drawbacks:

* Need to run `go build -a` to prevent caching
* Need to make sure orchestrion is compiled _first_

How comfortable are you with magic?

## Third-party Libraries

Add third-party library support via rewrite rules?

**Very clever!**

Like `gofmt -r`?

Rule file format that describes transformations.

For example:

```
vars:
handlerFunc func(...)

r.Get(path, handlerFunc) ->
r.Get(path, instrument.WrapHandlerFunc(handlerFunc)
```

## Future

There's a long way to go with this.

"Be careful with the magic"
