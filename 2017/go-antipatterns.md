Go Antipatterns - Edward Muller
==============

tl;dr Write code with orthogonality, simplicity, and readability in mind.

"Idiomatic Go"

# What is Idiomatic Go?

> Golang that adheres to the design principles of the Go language.

1) Orthogonality - a change in one place affects no others
2) Simplicity - No symbols tables, no inheritance, methods can be added to any type
3) Readability

# How do we know idiomatic Go when we see it?

Edward proposes "Go Anti-Patterns."

1. **Tiny package syndrome.** 
Lots of packages, usually containing a single file, typically missing tests.
Context and context-related packages often follow this anti-pattern.

2. **Premature exportation.**
Developers often taxonimize and break into overly DRY code, which leads to many small packages.
The package contents need to be exported. This leads to every type, constant, and variable
being exported (seems very much like Tiny Package Syndrome with over-exportation). 

**Don't export types, functions, structs, etc. until there is a need to do so.**

3. **Package util.**
In Go, package name have semantic meaning. The name of the package should describe its purpose,
not its contents. The only part of the package import path that matters is the rightmost name.
Util violates these principles.

4. **Config structs.**
A cmd package that contains one or more configuration types and functions. Telescoping functions
become an issue, and then this function becomes a single struct rather than a group of discrete structs.

5. **Pointer all the things**.
A pointer doesn't automatically mean that something will be faster. A pointer doesn't mean that less garbage will be created. It can guarantee that less data will be passed.
Pointing is sharing. "Don't communicate by sharing memory, share memory by communicating."

6. **It's a trap.**
Context-specific antipattern. Contexts can create synchronous APIs. It should be uncommon to surface channels in an API. See `net/http` for an example of a synchronous API
that uses asynchrony internally.

7. **If ... else.**
Cut needless `else`'s. Return early, return often. 

8. **Panic in a lib.**
Don't put panic in a lib.

9. **Large interfaces**.
"The bigger the interface, the weaker the abstraction." Don't write large interfaces.

10. **Naked return values**.
Don't have naked return values.

11. **Empty interface**.
Empty interface says nothing. Use the empty interface only when dealing with unknown data. Otherwise, tease out the interface you need.
