Generics in Go
Ian Lance Taylor


Why Generics?

    What do we mean, and why should we?

    Took 24 hours for the first complaint about generics!


Example: reverse slice

    Exactly the same apart from signature.

    Dynamic languages just don't specify the type.

    Most other statically typed languages use generics.

    In go: you can somewhat achieve this with interfaces.

    Another option could be to use the reflect package... but awkward.

    Or... write a code generator. Complicates the build.

    All of these approaches are "sufficiently awkward" that people just rewrite
    the function.

Why?

    Generic programming enables the representation of functions and data
    structures in a generic form, with types factored out.


Samples

    Read from a channel with a timeout.

    Combine two channels into another channel.

    Call functions in parallel, returning list of results.

    Call list of functions using a context, return result of the first
    function to finish.


Data Structures

    Sets
    Self balancing trees
    Multimaps
    Concurrent hash maps

    "Powerful building blocks"


Cost

    Every change has a cost.

    Maximize benefit and reduce cost.

    In Go, add orthogonal language features that can be combined freely.


Guidelines

    Minimize new concepts

    Complexity calls on writer of generic code, not the user

    Writer and user can work independently

    Short build times

    Preserve simplicity

    Only worth doing if "Go still feels like Go"


Proposed Design

    Implementation:

        func Reverse (type Element) (s []Element) {
            ...
        }

    Call with:

        Reverse(int)(s)

    Usually it can be deduced and we just use Reverse(s).


Contracts

    contract Sequence(T) {
        T string, []byte
    }


ToString

    Map a slice of things that implement fmt.Stringer to []string

    We have a contract that looks a lot like the Stringer interface{}.


Graph

    contracts can involve two types.

Min/Max

    Probably a new stdlib package called contracts.

    Use contracts.Ordered

Binary Tree

    ...

Current Status

    Type checking CL in progress.

    Looking for feedback on semantics.
