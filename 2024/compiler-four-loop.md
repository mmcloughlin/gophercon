# Exploring the Go Compiler: Adding a "four" loop

Let's add statements to the Go language:

* `four`: like a `for` but the post statement is executed 4 times
* `unless`: negated `if` statement

Compiler overview:

* Lexer/Parse
* Type Check
* IR Construction ("noding")
* Middle End
* ...

## Front-end

### Lexical Analysis and Parsing

Recursive descent parser converts to concrete syntax tree.

Recursive descent: top-down.

Concrete: _exact_ representation of the source code.

### Type Checking

* Name resolution
* Constant folding
* Type inference

To support new syntax: need to extend the type checker visitor to visit all
new nodes, and to ensure booleans at condition statements.

### IR ("noding")

Add and construct node types for our `four` and `unless` statements.

Code generation produces additional boilerplate.

## Middle-end

### Escape Analysis

Vistors need to be extended for the new node types.

### Walk

Two steps:

* Order: convert complex statements into simpler ones. Introduce temporaries.
* Desugar

For example, at this point we can convert `unless` into `if` with negated
condition.

### SSA Generation

Split into blocks. Each variable assigned once.

Also constructs control flow graph.

This is where we implement the logic of the 4 loop. We can emit the post
statement four times.

...

## Compiler Tooling

`GOSSAFUNC`

Demo of compiler pass output.
