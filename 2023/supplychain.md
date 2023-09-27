Security in GO:

* Well definined standard library

What can you trust?

* Does this package do what it claims to?
* Will it continue to be trustworthy?
* Are there any unpatched vulnerabilities?
* ...

Issues:

* Overly broad permissions
* Malicious updates
* Malicious packages "never any good to begin with" eg typoware


## Overly broad permissions

Log4j example.

Logging library with unexpected side effects!

## Malicious updates

Difficult to find examples in Go.

But someone cloned Go packages on mass, and added `init()` statements that
downloaded code.

"Github very good at cleaning up malicious code after the fact."

Malicious packages "never any good to begin with" eg typoware

## Key Concept: Capabilities

Principle of least permissions. Least capability: package should only execute
the capabilities to enable what it's trying to do.

Every Go package has an implicit set of _expected_ capabilities.

Types:

* Network access
* Filesystem access
* Reading/modifying system information
* Use of unsafe or reflect
* Running arbirary external code

## Mapping code to capabilities

* Code in the standard library maps to
* Static analysis to analyse the callgraph
* Transitively collect capabilities

Callgraphs eventually end at the standard library, which we have maps of
capabilities.

This allows us to **track when capabilities of code change**.

## Caveats

Static analysis will only allow us to detect a superset of a package's
capabilities.

...

## `capslock` tool

Performs this static analysis.

Integrated with `deps.dev`.

